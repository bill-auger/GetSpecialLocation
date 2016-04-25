
this segfault occurs when calling File::getSpecialLocation() statically
    if the corresponding path in ~/.config/user-dirs.dirs ends with a slash char
    which may be the case when user deletes a dir such as ~/Videos
    or if the file is edited manually

the underlying issue seems to be that the File::separatorString
    raw data pointer is null when referenced statically such as getting getSpecialLocation()

the segfault occurs when CharPointer_UTF8->getAndAdvance() dereferences the data pointer
    when a path ending in a slash char (e.g. /home/me/)
    is compared to File::separatorString in File::parseAbsolutePath()
    which does not happen if the path does not end in a slash

juce_File.cpp line 164 -->
```
     while (path.endsWithChar (separator) && path != separatorString)
```

this test case injects a pathological string into juce_linux_Files.cpp::resolveXDGFolder()
    and patches the defect in two different places for demonstration
    both in File::parseAbsolutePath() and CharPointer_UTF8->getAndAdvance()
