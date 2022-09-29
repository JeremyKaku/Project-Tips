```C#

 public static string MakeValidFileName(string text, string replacement = "_")
        {
            StringBuilder str=new StringBuilder();
            var invalidFileNameChars = System.IO.Path.GetInvalidFileNameChars();
            foreach (var c in text)
            {
                if (invalidFileNameChars.Contains(c))
                {
                    str.Append(replacement??"");
                }
                else
                {
                    str.Append(c);
                }
            }

            return str.ToString();
        }

```

```C#
public static string GetSafeFilename(string arbitraryString)
{
    var invalidChars = System.IO.Path.GetInvalidFileNameChars();
    var replaceIndex = arbitraryString.IndexOfAny(invalidChars, 0);
    if (replaceIndex == -1) return arbitraryString;

    var r = new StringBuilder();
    var i = 0;

    do
    {
        r.Append(arbitraryString, i, replaceIndex - i);

        switch (arbitraryString[replaceIndex])
        {
            case '"':
                r.Append("''");
                break;
            case '<':
                r.Append('\u02c2'); // '˂' (modifier letter left arrowhead)
                break;
            case '>':
                r.Append('\u02c3'); // '˃' (modifier letter right arrowhead)
                break;
            case '|':
                r.Append('\u2223'); // '∣' (divides)
                break;
            case ':':
                r.Append('-');
                break;
            case '*':
                r.Append('\u2217'); // '∗' (asterisk operator)
                break;
            case '\\':
            case '/':
                r.Append('\u2044'); // '⁄' (fraction slash)
                break;
            case '\0':
            case '\f':
            case '?':
                break;
            case '\t':
            case '\n':
            case '\r':
            case '\v':
                r.Append(' ');
                break;
            default:
                r.Append('_');
                break;
        }

        i = replaceIndex + 1;
        replaceIndex = arbitraryString.IndexOfAny(invalidChars, i);
    } while (replaceIndex != -1);

    r.Append(arbitraryString, i, arbitraryString.Length - i);

    return r.ToString();
}
```

https://support.microsoft.com/en-us/office/restrictions-and-limitations-in-onedrive-and-sharepoint-64883a5d-228e-48f5-b3d2-eb39e07630fa?ui=en-us&rs=en-us&ad=us

https://stackoverflow.com/questions/620605/how-to-make-a-valid-windows-filename-from-an-arbitrary-string


``` javascript

//验证上传文件的文件名是否合法
function validateFileName(fileName ){
	//var fileName = 'a.html';
	var reg = new RegExp('[\\\\/:*?\"<>|]');
	if (reg.test(fileName)) {
	    //"上传的文件名不能包含【\\\\/:*?\"<>|】这些非法字符,请修改后重新上传!";
	    return false;
	}
	return true;
}


```
