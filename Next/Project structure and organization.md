In the `app` directory, nested folders define route structure. Each folder represents a route segment that is mapped to a corresponding segment in a URL path.

However, even though route structure is defined through folders, a route is **not publicly accessible** until a `page.js` or `route.js` file is added to a route segment.

![[Pasted image 20250706202438.png|center]]

This means that **project files** can be **safely colocated** inside route segments in the `app` directory without accidentally being routable.

![[Pasted image 20250706202452.png|center]]

>**Good to know**: While you **can** colocate your project files in `app` you don't **have** to. If you prefer, you can [keep them outside the `app` directory](https://nextjs.org/docs/app/getting-started/project-structure#store-project-files-outside-of-app).

###  Private folders

Private folders can be created by prefixing a folder with an underscore: `_folderName`

This indicates the folder is a private implementation detail and should not be considered by the routing system, thereby **opting the folder and all its subfolders** out of routing.

![[Pasted image 20250706202643.png|center]]

Since files in the `app` directory can be [safely colocated by default](https://nextjs.org/docs/app/getting-started/project-structure#colocation), private folders are not required for colocation. However, they can be useful for:

- Separating UI logic from routing logic.
- Consistently organizing internal files across a project and the Next.js ecosystem.
- Sorting and grouping files in code editors.
- Avoiding potential naming conflicts with future Next.js file conventions.
  
### Route groups 
  
 Route groups can be created by wrapping a folder in parenthesis: `(folderName)`
 
 This indicates the folder is for organizational purposes and should **not be included** in the route's URL path.
 ![[Pasted image 20250706202841.png|center]]
