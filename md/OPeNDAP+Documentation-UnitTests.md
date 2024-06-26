# Writing Unit Tests for C++ using CppUnit

This page describes how to write unit tests for C++ classes using
CppUnit. The basic process is fairly simple: a *CppUnit::TestFixture* is
written that provides one or more tests for a specific class. However,
there are several tricks to writing tests that will make them much
easier to use. These help improve the interactive use of the tests,
simplifying finding problems when the tests are run as part of an
automated build, since these tests often become a kind of regression
test once a class has been developed.

Some parts of these instructions are specific to OPeNDAP and its source
code for libdap and the BES, but those parts are limited to code
examples and it is easy to see how the same ideas could be applied to
similar code that differs in its specifics.

## CppUnit basic layout

The example below shows a complete unit test written with *CppUnit*.
Using the helper macros streamlines writing the tests (but also
introduces some wrinkles we discuss later on).

When writing tests, make the default behavior as quiet as possible since
the tests will likely be run by an automated build. Use a debug macro or
switch to control output of instrumentation.

Here are the main sections of a CppUnit test:

### Header files

To get started, include header files so that you can subclass
TestFixture and use the CppUnit macros.

``` cpp
#include "config.h"        // Build by autoconf

#include <cppunit/TextTestRunner.h>
#include <cppunit/extensions/TestFactoryRegistry.h>
#include <cppunit/extensions/HelperMacros.h>
```

Then, include the header that defines the class you want to test, other
debug tools you use and whatever is needed for *getopt* in you
build/target environment. Also define static booleans for things like
debug switches and other run-time options. Adding a *debug* option is
good because these tests are often used to track down problems as well
as being used as part of automated builds. A debug option lets people
see what's wrong without gumming up the works when a machine runs the
tests.

``` cpp
#include "AttrTable.h"     // The class to test
#include "debug.h"         // Debug macros

#include "GetOpt.h"        // getopt C++ wrapper

static bool debug = false; // Set down in main()

#undef DBG
#define DBG(x) do { if (debug) (x); } while(false);
```

### Subclassing TestFixture

Subclass *CppUnit::TestFixture*. Use a name that makes it easy to
identify the class this code tests (e.g., *AttrTableTest* tests
*AttrTable*). The class should have a constructor and destructor and
*setUp()* and *tearDown()* methods. The latter will be run before and
after each test is run. The tests themselves are discussed in the next
section...

``` cpp

using namepsace CppUnit;

namespace libdap {

class AttrTableTest: public TestFixture {
    private:
        AttrTable *at1;

    public:
        AttrTableTest() { }
        ~AttrTableTest() { }

        void setUp()
        {
            at1 = new AttrTable;
        }

        void tearDown()
        {
            delete at1;
            at1 = 0;
        }

    // Tests and macros here...

};

} // namespace libdap
```

### Writing tests using macros

CppUnit tests are written in C++ as methods in the subclass of
*CppUnit::TestFixture*. Each test is a *void test_name()* method (i.e.,
a method that neither takes nor returns values. In the body of these
methods various macros can be used to test for various conditions and
evaluate (or indicate) success or failure. The two most common macros
used in the body of the test methods are:

CPPUNIT_ASSERT( *expr* )
Where *expr* should evaluate to true to indicate the test has behaved as
expected, or false otherwise. When *expr* is not true, this macro throws
an exception that indicates test failure and also ends the test. As with
*assert()*, this macro prints the failed assertion.

CPPUNIT_FAIL( *message* )
This macro causes a test to fail and prints *message*.

An example of these macros used in a test:

``` cpp
void test_method()
{
    try {
        // at1 is a class member, so it can be used in a method. Assume other code initialized at1...
        CPPUNIT_ASSERT( at1->name() == "AttrTable" );

        // It's common to call CPPUNIT_ASSERT() more than once
        CPPUNIT_ASSERT( at1->size() == 7 );
    }
    catch( Error &e ) {
        // CppUnit does not know about exceptions other than its own, so if the code under test throws,
        // it can lead to some odd messages. Catching the exceptions and then using CPPUNIT_FAIL is a
        // good approach to that issue.
        CPPUNIT_FAIL( "AttrTable threw an exception: " + e.get_error_message() )
    }
}
```

### Registering tests using macros

Once all of the test (methods) of the TestFixture are written, use more
macros to register the tests. Do this while still in the body of the
class that defines the subclass of teh TestFixture. Use the
**CPPUNIT_TEST( *test name* )** macro for each of the tests you want to
run. Bracket these calls with **CPPUNIT_TEST_SUITE( *TestFixture name*
)** and **CPPUNIT_TEST_SUITE_END()** As shown below. Only the tests
listed will be run, so removing one from the list will suppress it
needed. In addition to the **CPPUNIT_TEST()** macro there are two other
macros that may be useful:

CPPUNIT_TEST_FAIL( *testMethod* )
A test that is expected to fail - this is deprecated. Use
**CPPUNIT_ASSERT_ASSERTION_FAIL()** in the test method instead

CPPUNIT_TEST_EXCEPTION( *testMethod*, *ExceptionType* )
A test that is expected to throw a specific exception - this is
deprecated. Use **CPPUNIT_ASSERT_ASSERTION_FAIL()** instead

See [Writing Test
Fixture](http://cppunit.sourceforge.net/doc/cvs/group___writing_test_fixture.html)
for more information on these and other macros.

``` cpp
        CPPUNIT_TEST_SUITE( AttrTableTest );

        CPPUNIT_TEST(clone_test);
        CPPUNIT_TEST(find_container_test);
        CPPUNIT_TEST(get_parent_test);
        CPPUNIT_TEST(recurrsive_find_test);
        CPPUNIT_TEST(find_test);
        CPPUNIT_TEST(copy_ctor);
        CPPUNIT_TEST(assignment);
        CPPUNIT_TEST(erase_test);
        CPPUNIT_TEST(names_with_spaces_test);
#if 0
        CPPUNIT_TEST(print_xml_test);
#endif

        CPPUNIT_TEST_SUITE_END();
```

CPPUNIT_TEST_SUITE_REGISTRATION( *test class name* )
Use this after the close of the test class. This macro adds all of the
tests in the TestFixture to the global registry. Code in *main()* will
use this to bind the tests to an instance of *TestRunner*. This macro
follows the close of the TestFixture but should not be part of *main()*.
Also, if the TestFixture is in a namespace, this macro should be in the
namespace, too.

### Main

### Building the code

<font color="red">WARNING: Some of what follows is OLD and WRONG.
Especailly the parts about running the tests by name.</font>

## Running tests by name

To run a test by name, where 'test' means one of the methods in the
TestFixture class, pass the method name to TestRunner::run(). For
example, if you have a TestRunner named *runner*, a TestFixture class
named *MyTests* and it has methods *void test_1() {}* and *void test_2()
{}*, you would run those by calling *runner.run("myTest::test_1")* and
*runner.run(:myTes::test_2")*. This will work if the test source file
uses the macro *CPPUNIT_TEST_SUITE( MyTest );* to declare the TestFixure
and *CPPUNIT_TEST(test_1);*, ..., to declare the individual tests. Note
that after the TestFixure class is defined, the macro
*CPPUNIT_TEST_SUITE_REGISTRATION(MyTest);* must be used.

## Using command line switches to control output

Look in the example to see how I use Gnu's GetOpt to process command
line options. In the code, '-d' is used to turn on debugging. When on, a
static global is set to true and a macro writes various instrumentation
to stderr. Here's the global (file scope) and macro:

``` cpp
static bool debug = false;

#undef DBG
#define DBG(x) do { if (debug) (x); } while(false);
```

The *\#undef DBG* is there because the *\#define* will redefine the
*DBG* macro, changing it from the definition provided by *debug.h*.

and the code to process the switch and call tests if they are named on
the command line:

``` cpp
int main(int argc, char*argv[]) {
    CppUnit::TextTestRunner runner;
    runner.addTest(CppUnit::TestFactoryRegistry::getRegistry().makeTest());

    GetOpt getopt(argc, argv, "d");
    char option_char;
    while ((option_char = getopt()) != EOF)
        switch (option_char) {
        case 'd':
            debug = 1;  // debug is a static global
            break;
        default:
            break;
        }

    bool wasSuccessful = true;
    string test = "";
    int i = getopt.optind;
    if (i == argc) {
        // run them all
        wasSuccessful = runner.run("");
    }
    else {
        while (i < argc) {
            test = string("D4DimensionsTest::") + argv[i++];

            wasSuccessful = wasSuccessful && runner.run(test);
        }
    }

    return wasSuccessful ? 0 : 1;
}
```

The first loop processes the option; the if will call runner.run with ""
which runs all the tests if there are no more arguments or will assume
that remaining arguments are test names and will iterate over them,
calling each in turn.

Note: The "-d" switch and calling the tests by name *will not work with
make*. You have to run the test code from the command line by hand. If
your unit test file is named *testy.cc* the you'll need to change
directory to the unit-tests dir and do a *./testy -d testName* to see
the effects of these changes.

## Putting it all together

``` cpp
#include "config.h"

#include <cppunit/TextTestRunner.h>
#include <cppunit/extensions/TestFactoryRegistry.h>
#include <cppunit/extensions/HelperMacros.h>

#include <GetOpt.h> // Part of libdap

//#define DODS_DEBUG

#include "D4Dimensions.h"
#include "XMLWriter.h"

#include "Error.h"
#include "debug.h"

#include "testFile.h"
#include "test_config.h"

using namespace CppUnit;
using namespace std;
using namespace libdap;

static bool debug = false;

#undef DBG
#define DBG(x) do { if (debug) (x); } while(false);

class D4DimensionsTest: public TestFixture {
private:
    XMLWriter *xml;
    D4Dimensions *d;

public:
    D4DimensionsTest() {
    }

    ~D4DimensionsTest() {
    }

    void setUp() {
        d = new D4Dimensions;
        xml = new XMLWriter;
    }

    void tearDown() {
        delete xml;
        delete d;
    }

    void test_print_copy_ctor() {
        d->add_dim_nocopy(new D4Dimension("first", 10));
        d->add_dim_nocopy(new D4Dimension("second", 100));
        d->add_dim_nocopy(new D4Dimension("third"));

        D4Dimensions lhs(*d);

        lhs.print_dap4(*xml);
        string doc = xml->get_doc();
        string baseline = readTestBaseline(string(TEST_SRC_DIR) + "/D4-xml/D4Dimensions_3.xml");
        DBG(cerr << "test_print_copy_ctor: doc: " << doc << endl);
        DBG(cerr << "test_print_copy_ctor: baseline: " << baseline << endl);
        CPPUNIT_ASSERT(doc == baseline);
    }

    CPPUNIT_TEST_SUITE( D4DimensionsTest );

        CPPUNIT_TEST(test_print_empty);

        // CPPUNIT_TEST_EXCEPTION( test_error, Error );

    CPPUNIT_TEST_SUITE_END();
};

CPPUNIT_TEST_SUITE_REGISTRATION(D4DimensionsTest);

int main(int argc, char*argv[]) {
    CppUnit::TextTestRunner runner;
    runner.addTest(CppUnit::TestFactoryRegistry::getRegistry().makeTest());

    GetOpt getopt(argc, argv, "d");
    char option_char;

    while ((option_char = getopt()) != EOF)
        switch (option_char) {
        case 'd':
            debug = 1;  // debug is a static global
            break;
        default:
            break;
        }

    bool wasSuccessful = true;
    string test = "";
    int i = getopt.optind;
    if (i == argc) {
        // run them all
        wasSuccessful = runner.run("");
    }
    else {
        while (i < argc) {
            test = string("D4DimensionsTest::") + argv[i++];

            wasSuccessful = wasSuccessful && runner.run(test);
        }
    }

    return wasSuccessful ? 0 : 1;
}
```