Determine file occupancy status

```C#

public static bool isFileInUse(string fileName)
{
  bool inUse = true;
  FileStream fs = null;
  
  try{
      fs = new FileStream(fileName, FileMode.Open, FileAccess.Read, FileShare.None);
      inUse = false;
  }
  catch
  {
  }
  finally
  {
    if(fs != null)
      fs.Close();
  }
  
  return inUse;
}

```
