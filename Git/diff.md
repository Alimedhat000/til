
`git diff` shows what is the current changes made  

but to make it much better removing those `+` and `-` and just using the colors you can add this snippet to your global git config 

```ini
# ~/.config/git/config
[diff]
  context = 3
  renames = copies
  interHunkContext = 10

[pager]
  diff = diff-so-fancy | $PAGER

[diff-so-fancy]
  markEmptyLines = false
```

This Git config does the following:

- Shows 3 lines of context around diffs.
- Treats renamed files as copies.
- Adds more context (10 lines) between diff hunks.
- Uses `diff-so-fancy` with your pager for prettier diffs.
- Disables marking empty lines in `diff-so-fancy`.
  
and now add 