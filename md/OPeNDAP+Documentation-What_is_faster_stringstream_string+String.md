## Question

I have two string objects:string str_1, str_2. I want to concatenate to
them. I can use two methods:

method 1:

    std::stringstream ss;
    ss << "hello"<< "world"；
    const std::string dst_str = std::move(ss.str());

method 2:

    std::string str_1("hello");
    std::string str_2("world");
    const std::string dst_str = str_1 + str_2;

Because the string's buffer is read only, when you change the string
object, its buffer will destroy and create a new one to store new
content. So method 1 is better than method 2? Is my understanding
correct?

## Answer

From StackOverflow
(https://stackoverflow.com/questions/30254175/is-stringstream-better-than-strings-operator-for-string-objects-concatenati)

stringstreams are complex objects compared to simple strings. Everythime
you use method 1, a stringstream must be constructed, and later
destructed. If you do this millions of time, the overhead will be far
from neglectible.

The apparently simple

    ss << str_1 << str_2

is in fact equivalent to

    std::operator<<(sst::operator<<(ss, str_1), str_2);

which is not optimized for in memory concatenation, but common to all
the streams.

I've done a small benchmark :

- In debug mode, method 2 is almost twice as fast as method1.

<!-- -->

- In optimized build (verifying in the assembler file that nothing was
  optimized away), it's more then 27 times faster.