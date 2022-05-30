
把给定的那个文件看作是无类型的二进制文件，然后顺序地读出这个文件的每一个字节，如果文件里有一个字节的值等于0，那么这个文件就不是文本文件；
反之，如果这个文件中没有一个字节的值是0的话，就可以判定这个文件是文本文件了。

Treat the given file as an untyped binary file, and read each byte of the file sequentially. 
If there is a byte in the file whose value is equal to 0, then the file is not a text file;
otherwise, If there is no byte in the file whose value is 0, it can be determined that the file is a text file.
https://www.cnblogs.com/criedshy/archive/2010/05/24/1742918.html

others:
https://stackoverflow.com/questions/910873/how-can-i-determine-if-a-file-is-binary-or-text-in-c
https://stackoverflow.com/questions/4744890/c-sharp-check-if-file-is-text-based


```C#

/// <summary>
/// Checks the file is textfile or not.
/// </summary>
/// <param name="fileName">Name of the file.</param>
/// <returns></returns>
public static bool CheckIsTextFile(string fileName)
        {
            FileStream fs = new FileStream(fileName, FileMode.Open, FileAccess.Read);
bool isTextFile=true;
try
            {
int i = 0;
int length = (int)fs.Length;
byte data;
while (i < length && isTextFile)
                {
                    data = (byte)fs.ReadByte();
                    isTextFile = (data != 0);
                    i++;
                }
return isTextFile;
            }
catch (Exception ex)
            {
throw ex;
            }
finally
            {
if (fs != null)
                {
                    fs.Close();
                }
            }
        }

```
