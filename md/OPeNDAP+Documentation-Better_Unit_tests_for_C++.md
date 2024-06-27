We use CPPUNIT for C++ unit tests. But most of our tests don't take
advantage of CPPUNIT's best feature - its extensive set of test and
assertion macros. This how-to explores some of the macros using a real
test that was rewritten to be more useful and easier to read (and a bit
shorter).

## Parts of a CPPUNIT test suite

We want to write *test suites* and not tests because it is better to run
a collection tests where each test runs regardless of whether the
previous tests pass or fail. A single test can easily hide multiple
(possibly related) problems because it stops running at the first
failure. But it's hard to write tests that look for exceptions and other
complex conditions without repeating lots of 'boilerplate' code. CPPUNIT
macros to the rescue.

It is important to make each test in the test suite *independent of the
other tests*.

But first, the parts of a test suite:

Headers: The CPPUNIT headers, the header for the class under test, headers that header needs and other stuff for the tests
Class definition: Each test suite is a class. The class typically contains an instance (or pointer to) of the class to be tested.
Class constructor and destructor: Often these can be set to the default. If they are used to initialize class members, that initialization will be done *once for the entire test suite*.
Setup and Teardown methods: These methods are run before and after *each test in the test suite*.
Test suite definition: This section includes the CPPUNIT macros that define what is run and the implementation of the methods that are called by those macros.
A main function to run the the tesuite

## More information on CPPUNIT macros

Here are tables of the macros available in CPPUNIT

### Macros for use in test methods

CPPUNIT_ASSERT(condition): Assertions that a condition is true.
CPPUNIT_ASSERT_MESSAGE(message, condition): Assertion with a user specified message.
CPPUNIT_FAIL(message): Fails with the specified message.
CPPUNIT_ASSERT_EQUAL(expected, actual): Asserts that two values are equals.
CPPUNIT_ASSERT_EQUAL_MESSAGE(message, expected, actual): Asserts that two values are equals, provides additional message on failure.
CPPUNIT_ASSERT_DOUBLES_EQUAL(expected, actual, delta): Macro for primitive double value comparisons.
CPPUNIT_ASSERT_DOUBLES_EQUAL_MESSAGE(message, expected, actual, delta): Macro for primitive double value comparisons, setting a user-supplied message in case of failure.
CPPUNIT_ASSERT_THROW(expression, ExceptionType): Asserts that the given expression throws an exception of the specified type.
CPPUNIT_ASSERT_THROW_MESSAGE(message, expression, ExceptionType): Asserts that the given expression throws an exception of the specified type, setting a user supplied message in case of failure.
CPPUNIT_ASSERT_NO_THROW(expression): Asserts that the given expression does not throw any exceptions.
CPPUNIT_ASSERT_NO_THROW_MESSAGE(message, expression): Asserts that the given expression does not throw any exceptions, setting a user supplied message in case of failure.
CPPUNIT_ASSERT_ASSERTION_FAIL(assertion)
CPPUNIT_ASSERT_THROW( assertion, CPPUNIT_NS::Exception ) : Asserts that an assertion fail.
CPPUNIT_ASSERT_ASSERTION_FAIL_MESSAGE(message, assertion)
CPPUNIT_ASSERT_THROW_MESSAGE( message, assertion, CPPUNIT_NS::Exception ): Asserts that an assertion fail, with a user-supplied message in case of error.
CPPUNIT_ASSERT_ASSERTION_PASS(assertion)
CPPUNIT_ASSERT_NO_THROW( assertion ): Asserts that an assertion pass.
CPPUNIT_ASSERT_ASSERTION_PASS_MESSAGE(message, assertion)
CPPUNIT_ASSERT_NO_THROW_MESSAGE( message, assertion ): Asserts that an assertion pass, with a user-supplied message in case of failure.

See the full documentation
[here](http://cppunit.sourceforge.net/doc/cvs/group___assertions.html#ga2)

### Macros for defining test suites

CPPUNIT_TEST_SUITE(ATestFixtureType): Begin test suite.
CPPUNIT_TEST_SUB_SUITE(ATestFixtureType, ASuperClass): Begin test suite (includes parent suite).
CPPUNIT_TEST_SUITE_END(): End declaration of the test suite.
CPPUNIT_TEST_SUITE_END_ABSTRACT(): End declaration of an abstract test suite.
CPPUNIT_TEST_SUITE_ADD_TEST(test) context.addTest( test ): Add a test to the suite (for custom test macro).
CPPUNIT_TEST(testMethod): Add a method to the suite.
CPPUNIT_TEST_EXCEPTION(testMethod, ExceptionType): Add a test which fail if the specified exception is not caught.
CPPUNIT_TEST_FAIL(testMethod): Adds a test case which is expected to fail.
CPPUNIT_TEST_EXCEPTION(testMethod,: CPPUNIT_NS::Exception): This test is expected to throw the named exception
CPPUNIT_TEST_SUITE_ADD_CUSTOM_TESTS(testAdderMethod): Adds some custom test cases.
CPPUNIT_TEST_SUITE_PROPERTY(APropertyKey, APropertyValue): Adds a property to the test suite builder context.
CPPUNIT_TEST_SUITE_REGISTRATION(ATestFixtureType)
CPPUNIT_TEST_SUITE_NAMED_REGISTRATION(ATestFixtureType, suiteName): Adds the specified fixture suite to the specified registry suite.
CPPUNIT_REGISTRY_ADD(which, to)
CPPUNIT_REGISTRY_ADD_TO_DEFAULT(which)

See the full documentation
[here](http://cppunit.sourceforge.net/doc/cvs/_helper_macros_8h.html)

## An example unit test

Here's an example (a slimmed down version of
bes/dispatch/unit-tests/checkT.cc. **But see Section 4 for a more
concise way to handle this!**

    // HEADERS
    #include <cppunit/TextTestRunner.h>
    #include <cppunit/extensions/TestFactoryRegistry.h>
    #include <cppunit/extensions/HelperMacros.h>

    #include "config.h"

    #include "BESUtil.h"

    #include "BESForbiddenError.h"
    #include "BESNotFoundError.h"

    // This pulls in macros for pathnames, e.g., TEST_BUILD_DIR, which is used to
    // make tests that use absolute paths but are independent of where they are run.

    #include "test_config.h"

    using namespace std;
    using namespace CppUnit;

    // These globals are used for the debug command line switches. See main().

    static bool debug = false;
    static bool debug2 = false;

    #undef DBG
    #define DBG(x) do { if (debug) (x); } while(false)

    #undef DBG2
    #define DBG2(x) do { if (debug2) (x); } while(false)

    class checkT: public TestFixture {

    public:
        checkT() = default;

        virtual ~checkT() = default;

        virtual void setUp()
        {
            ...
        }

        // Define the test suite
        CPPUNIT_TEST_SUITE(checkT);

        // See below for more macros, e.g., CPPUNIT_TEST_FAIL(testMethod) or
        // CPPUNIT_TEST_EXCEPTION(testMethod, ExceptionType)

        CPPUNIT_TEST(simple_test);
        CPPUNIT_TEST(another_simple_test);
        CPPUNIT_TEST(test_slash_path);
        CPPUNIT_TEST(test_sym_link_sym_links_not_allowed);

        CPPUNIT_TEST_SUITE_END();

        // These methods make up the tests suite. There are lots of macros you can
        // use to simplify these methods.

        void simple_test()
        {
            DBG(cerr << "test something simple using an assert" << endl);
            CPPUNIT_ASSERT(string("Hyrax").length() == 5);
        }

        void another_simple_test()
        {
            DBG(cerr << "test something" << endl);
            CPPUNIT_ASSERT_EQUAL_MESSAGE("string("Hyrax").length() should return 5", 5, string("Hyrax").length())
        }

        void test_slash_path()
        {
            DBG(cerr << "checking /" << endl);
            CPPUNIT_ASSERT_NO_THROW_MESSAGE("checking /", BESUtil::check_path("/", "./", true));
        }

        ...

        void test_sym_link_sym_links_not_allowed()
        {
            DBG(cerr << "checking /testdir/link_to_nc/, follow_syms = false" << endl);
            CPPUNIT_ASSERT_THROW_MESSAGE("checking /testdir/link_to_nc/, follow_syms = false",
                                         BESUtil::check_path("/testdir/link_to_nc/", "./", false),
                                         BESForbiddenError);
        }

        ...
    };

    CPPUNIT_TEST_SUITE_REGISTRATION(checkT);

    // Here is a sample main() that supports command line arguments and running individual tests by name

    int main(int argc, char *argv[]) {
        int option_char;
        while ((option_char = getopt(argc, argv, "dDh")) != EOF) {
            switch (option_char) {
                case 'd':
                    debug = true;  // debug is a static global
                    break;
                case 'D':
                    debug2 = true;  // debug is a static global
                    break;
                case 'h': {     // help - show test names
                    cerr << "Usage: checkT has the following tests:" << endl;
                    const std::vector<Test *> &tests = checkT::suite()->getTests();
                    unsigned int prefix_len = checkT::suite()->getName().append("::").length();
                    for (auto test: tests) {
                        cerr << test->getName().replace(0, prefix_len, "") << endl;
                    }

                    return 0;
                }
                default:
                    break;
            }
        }

        argc -= optind;
        argv += optind;

        CppUnit::TextTestRunner runner;
        runner.addTest(CppUnit::TestFactoryRegistry::getRegistry().makeTest());

        bool wasSuccessful = true;
        string test = "";
        if (0 == argc) {
            // run them all
            wasSuccessful = runner.run("");
        }
        else {
            int i = 0;
            while (i < argc) {
                DBG(cerr << "Running " << argv[i] << endl);
                test = checkT::suite()->getName().append("::").append(argv[i++]);
                wasSuccessful = wasSuccessful && runner.run(test);
            }
        }

        return wasSuccessful ? 0 : 1;
    }

## New support for main()

Writing main() as shown above is tedious. Examining a number of these
tests reveals that main() changes little except for the name of the
class to be used for the tests. This indicates that maybe a templated
function could be used, and that is indeed the case. In
bes/modules/run_tests_cppunit.h you'll find a function that can be used
like this:

    int main(int argc, char *argv[])
    {
        return bes_run_tests<HistoryUtilsTest>(argc, argv, "cerr,bes") ? 0: 1;
    }

And covers the functionality of the handwritten main() used above except
that -D is not supported (it could be added to the template) and -b is
supported.

For the bes_run_tests<CLASS>() function, the first two arguments do the
same thing as with the 'normal' main(). The third argument is used with
the -b option. When that option is used at runtime, the string (e.g.,
"cerr,bes") is passed to the BESDEBUG setup function so that the BES's
internal instrumentation is turned on. It's chatty but useful

## Limiting the code actually tested

Suppose we have:

        void test_logical_chunks_1() {
            d_dmz.reset(new DMZ(chunked_fourD_dmrpp));
            DmrppTypeFactory factory;
            DMR dmr(&factory);
            d_dmz->build_thin_dmr(&dmr);

            // Given a thin DMR, load in the attributes of the first variable
            BaseType *btp = *(dmr.root()->var_begin());
            auto *dc = dynamic_cast<DmrppCommon *>(btp);
            CPPUNIT_ASSERT(dc);

            d_dmz->load_chunks(btp);

            vector<unsigned long long> array_dim_sizes;
            array_dim_sizes.push_back(40);
            array_dim_sizes.push_back(40);
            array_dim_sizes.push_back(40);
            array_dim_sizes.push_back(40);
            size_t num_logical_chunks = DMZ::logical_chunks(array_dim_sizes, dc);

            CPPUNIT_ASSERT(num_logical_chunks == 16);
        }

This will test the DMZ::logical_chunks() method, but it also runs lots
of other code, which has to be in place before we test
DMZ::logical_chunks(). Suppose we wrote this test as:

        void test_logical_chunks_1() {
            unique_ptr<DmrppCommon> dc(new DmrppCommon);

            vector<unsigned long long> cds_values = { 20, 20, 20, 20};
            dc->set_chunk_dimension_sizes(cds_values);

            vector<unsigned long long> array_dim_sizes;
            array_dim_sizes.push_back(40);
            array_dim_sizes.push_back(40);
            array_dim_sizes.push_back(40);
            array_dim_sizes.push_back(40);

            size_t num_logical_chunks = DMZ::logical_chunks(array_dim_sizes, dc.get());

            CPPUNIT_ASSERT(num_logical_chunks == 16);
        }

Now, in addition to the code to test, we call only the DmrppCommon
default constructor (which does nothing but set some default values for
class members) and the DmrppCommon::set_chunk_dimension_sizes() method,
which is a simple vector copy method. We could eliminate that call as
well, using a **mock** class, but in this case it seems like overkill.