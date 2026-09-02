# Tools Usage Notes

## Git in Cross-Platform Teams

When team members use different operating systems, agree on Git conventions early and be careful with changes that may behave differently on Windows, macOS, and Linux.

### Line Endings

Windows commonly uses `CRLF` line endings, while macOS and Linux commonly use `LF`. Inconsistent line endings can make Git show an entire file as changed even when its content has not changed.

Agree to use `LF` line endings for text files and add a `.gitattributes` file in the repository root:

```gitattributes
* text=auto eol=lf
```

This lets Git normalize text files consistently for every team member. Discuss any exceptions before adding them, such as files that must use `CRLF` for a specific tool.

### File Names and Letter Case

Windows file systems are usually case-insensitive, whereas Linux file systems are case-sensitive. For example, `UserService.cs` and `userservice.cs` are different files on Linux but may be treated as the same file on Windows.

Use a consistent file-naming convention and avoid names that differ only by letter case. Case-only renames can also be missed or handled inconsistently on Windows. When renaming a file, use `git mv` and ask teammates to pull the change before making edits to that file.