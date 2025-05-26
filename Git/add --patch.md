instead of pushing a whole bunch of files at once without context you can use `git add --patch` to fire up a wizard that goes over each hunk of changes and it would ask you to:
- `y` – stage this hunk 
- `n` – skip this hunk
- `s` – split this hunk into smaller pieces
- `e` – manually edit the hunk
- `q` – quit

You can decide exactly what to stage—giving you more **control** over what ends up in your commit.

![[Pasted image 20250526153056.png|center|500]]
