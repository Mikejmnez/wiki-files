# Error Handling

The FreeForm ND error handling system captures errors, such as improper
usage, code problems, and system errors, and places them in an error
queue. For each error captured, error type and a short message are
placed in the message queue. If a fatal error occurs, the program stops
executing and displays all error messages in the queue.

## Error Messages

The following is a list of some possible error messages with suggestions
for corrections.

Problem opening, reading, or writing to file : Check that all file names and paths are correct.
Problem making format : Make sure there is a format file describing the data file formats. Check that input and output format descriptions in the format file accurately describe the data.
Problem making header format : If a header exists in the data file, it must be described in a format file. Check that the header description accurately describes the header in your data file.
Problem getting value :
Problem processing variable list : The data formats may not be described correctly or there may be some inconsistencies in the data. Check also for unprintable characters at the end of the data file.
File length / Record length mismatch :
Record Length or CR Problem : This usually happens because the input format description is not correct. Make sure the format description's last position is the last character before the end-of-line character. If you have a header, make sure it is described correctly. The header's length must include all characters up until the last end-of-line-character before the data begins.
Binary Overflow : Try using a larger output variable type such as a long instead of a short. Be sure you have given enough space for the values to be written. See Chapter for more information.
Variable not found : The variable names in your output format must match the variable names in the input format unless you are using conversion variables.
Data Overflow : Data overflow does not usually cause a fatal error and FreeForm ND functions try to anticipate them. If overflow occurs for a particular value, \*\*\*'s are written to that value's location. If you find these in your output, check your variable positions and precision. Increase field width or use a "larger" data type. Be sure the output format specifies space for the output variable. For instance, FreeForm ND adds a leading zero in front of decimal points. If the original data did not have a leading zero, the output will have one more digit than the input.
Insufficient memory allocation : The application has run out of memory. Try using the -b (local buffer size) option, or modify autoexec.bat and config.sys and comment out devices, TSR's, etc.