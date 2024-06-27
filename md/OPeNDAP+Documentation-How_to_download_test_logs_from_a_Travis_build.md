All of our CI/CD builds for both libdap4 and the BES run tests and save
the results of the tests to an S3 bucket named **opendap.travis.tests**.

Here's how to download the collected log files from those tests.

1.  Scroll to the end of one of the travis builds for these projects.
2.  At the very end you will see a line that says ...
3.  Expand that line by clicking the triangle to the left.
4.  Copy the S3 URL.
5.  Using the AWS Command Line Tool, copy the file to your local
    computer. For example: **aws s3 cp
    s3://opendap.travis.tests/bes-autotest-4389.2-logs.tar.gz .**
6.  Make a new directory, move the tarball into that new (otherwise
    empty) directory, and expand the tarball (if you don't do this in a
    new directory, it can litter your CWD with lots of files and dirs).
7.  Now, find the test results you want to see.

Here's a screenshot of the line at the very bottom of a travis build log
--\> <img src="Travis_test_log_line_2023-04-19.png"
title="Travis_test_log_line_2023-04-19.png" width="500"
alt="Travis_test_log_line_2023-04-19.png" />

### Caveats

This bucket is not open to anyone, you will need AWS credentials to
download from it.

It is possible to visit the bucket using the AWS console (assuming you
have credentials) and browse and download pretty much any build.

We periodically clean this bucket because it does cost money to store
data there.

If you restart a build, the logs from the restart will overwrite the
logs from the original run since the tarball will have the same name and
we do not have 'S3 object versioning turned on.'