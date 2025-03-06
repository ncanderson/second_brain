202407181130
Status: #idea
Tags: #programming #work 

# meld
Shay
You can also configure git to invoke a third party diff tool for merges in your ~/.gitconfig. For example I have my git set up to use `meld` for diffs and merges:  
```
[diff]
  tool = meld

[difftool]
  prompt = false

[difftool "meld"]
  cmd = meld "$REMOTE" "$LOCAL"

[merge]
  tool = meld
```

Shay

~~and then to merge using the tool run~~ `~~git mergetool ...~~` ~~instead of `git merge ...`~~ (this is actually not quite right) still use `git merge ...` to perform the merge, but then if it reports merge errors you'd then call `git mergetool` to invoke your graphical tool for resolving the merge errors (or `git difftool` if you just want to see a diff between branches) (edited) 

Jaine Perotti 

nice, thank you both for the suggestions, that's kind of what I was looking for - a way to easily visualize/make the changes.  I already have meld, which I've used to compare versions, but didn't know I could use it this way.

Shay

in my ~/.bashrc I defined a function for performing git merges that will merge on the command line if possible then fall back to the merge tool if the merge reports conflicts:

```bash
gitmerge()
{
  git merge "$@" || git mergetool
}
```

# References

