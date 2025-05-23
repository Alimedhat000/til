
React Router is a library that enables "client side routing". Client side routing allows your app to update the URL from a link click without making another request for another document from the server. Instead, your app can immediately render some new UI and make data requests with `fetch` to update the page with new information.
## Create Router

Replace the `App` in the `Main.tsx` with a Router from react router

```jsx
import React from 'React'
import ReactDOM from 'react-dom/client'
import {CreateBrowserRouter, RouterProvider} from 'react-router-dom'
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import { createBrowserRouter, RouterProvider } from 'react-router-dom'
import './index.css'

const router = createBrowserRouter([]);

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <RouterProvider router={router} />
  </StrictMode>,
)
```

`createBrowserRouter` is the most popular and the recommended way of creating a router by __React Router__ Docs, But there's other ways.
These are all the routers in v6.4:
- [`createBrowserRouter`](https://reactrouter.com/6.28.0/routers/create-browser-router)
- [`createMemoryRouter`](https://reactrouter.com/6.28.0/routers/create-memory-router)
- [`createHashRouter`](https://reactrouter.com/6.28.0/routers/create-hash-router)
- [`createStaticRouter`](https://reactrouter.com/6.28.0/routers/create-static-router)

Then we add the routes in the `createBrowserRouter` function

```jsx
const router = createBrowserRouter([
	{	
		path = '/',
		element: <HomePage/>	
	},
	{
		path = '/profiles',
		element: <Profiles/>
	}
]);
```

Now our page will render The `HomePage` component when the route is `'/'` , And `Profiles` when the the route is `'/profiles'`.

## Error Element

You'll probably thinking: 
	
	But what if the route isn't mapped to any component?

Then We can use the `ErrorElement`

```jsx
const router = createBrowserRouter([
	{	
		path = '/',
		element: <HomePage/>,	
		errorElement: <div>404 Not Found<div>
	},
	{
		path = '/profiles',
		element: <Profiles/>
	}
]);
```

Now any error path would load that `errorElement`.

also if you create a dedicated error element -which is advised to do- you can use the `useRouteError` function to get the exact error message and status.

```JSX
import { useRouteError } from "react-router-dom";

export default function ErrorPage() {
  const error = useRouteError();
  console.error(error);

  return (
    <div id="error-page">
      <h1>Oops!</h1>
      <p>Sorry, an unexpected error has occurred.</p>
      <p>
        <i>{error.statusText || error.message}</i>
      </p>
    </div>
  );
}

```
## Links 

In react router to handle links that utilize the SPA behavior
You should use `<Link to= "/Example"> </Link>`

## Dynamic Paths

If you want to create a route for each profile dynamically, you can use route parameters. In React Router, you define dynamic segments in the path using a colon (`:`) before the parameter name.

For example, to create a dynamic profile page for different users, modify the `createBrowserRouter` function like this:

```JSX
const router = createBrowserRouter([
	{	
		path: '/',
		element: <HomePage/>,	
		errorElement: <div>404 Not Found</div>
	},
	{
		path: '/profiles',
		element: <Profiles/>
	},
	{
		path: '/profiles/:id',
		element: <ProfilePage/>
	}
]);
```

In this case, `:id` is a route parameter that represents a unique profile ID. When navigating to `/profiles/1`, `/profiles/2`, etc., the `ProfileDetail` component will be rendered.

### Accessing Route Parameters

To access the dynamic `id` parameter inside the `ProfileDetail` component, use the `useParams` hook from `react-router-dom`:

```JSX
import { useParams } from 'react-router-dom';

const ProfilePage = () => {
	const params = useParams();
	
	return (
		<div>
			<h2>Profile ID: {params.id}</h2>
		</div>
	);
};
```

in TypeScript we can type the `useParams` return to take advantage of our IDE auto-complete like

```JSX
const params = useParams<{id: string}>();
```


## Nested Routes (Children Routes)

React Router allows you to **nest** routes inside one another, meaning a parent route can render child routes inside it. This is useful for layouts or pages with shared components like a sidebar, header, or navigation.

To define child routes, we use the `children` property in `createBrowserRouter`.

```JSX
const router = createBrowserRouter([
	{
		path: '/profiles',
		element: <Profiles />, // Parent route
		children: [
			{ path: 'profiles/:id', element: <ProfilePage /> },
		],
	},
]);
```

Now in `<Profiles />` we can render these _children_ inside the Parent route using the `Outlet` component.

```JSX
import { Outlet, Link } from 'react-router-dom';

const Profiles = () => {
	return (
		<div>
			<h2>Profiles</h2>
			<ul>
				<li><Link to="1">Profile 1</Link></li>
				<li><Link to="2">Profile 2</Link></li>
				<li><Link to="3">Profile 3</Link></li>
			</ul>
			<hr />
			<Outlet /> {/* This will render child routes */}
		</div>
	);
};
```

## Loader

a `loader` is a function that allows you to **fetch data before rendering a route**. It helps in **server-side fetching** and **preloading data**, ensuring that your component has all the necessary information before it is displayed.

```JSX
import { createBrowserRouter } from "react-router-dom";
import ProfilePage from "./ProfilePage";

const router = createBrowserRouter([
  {
    path: "/profiles/:id",
    element: <ProfilePage />,
    loader: async ({ params }) => {
      const response = await fetch(`https://api.example.com/profiles/${params.id}`);
      if (!response.ok) throw new Error("Profile not found");
      return response.json(); // Data is passed to the component
    },
  },
]);
```

Then access this data in the component using `useLoaderData`

```JSX
import { useLoaderData } from "react-router-dom";

const ProfilePage = () => {
  const profile = useLoaderData(); // Fetches the preloaded data

  return (
    <div>
      <h2>{profile.name}</h2>
      <p>Email: {profile.email}</p>
      <p>Age: {profile.age}</p>
    </div>
  );
};
```


## Actions

An `action` in React Router is a function that **handles form submissions for a route before rendering the component**. It allows you to modify or save data (like submitting to an API or updating a database) before the UI updates.

```JSX
import { createBrowserRouter } from "react-router-dom";
import ContactForm from "./ContactForm";

const router = createBrowserRouter([
  {
    path: "/contact",
    element: <ContactForm />,
    action: async ({ request }) => {
      const formData = await request.formData();
      const name = formData.get("name");
      const email = formData.get("email");

      console.log("Form submitted:", { name, email });

      return { success: true };
    },
  },
]);

```


```jsx
import { Form } from "react-router-dom";

const ContactForm = () => {
  return (
    <Form method="post">
      <label>
        Name:
        <input type="text" name="name" required />
      </label>
      <label>
        Email:
        <input type="email" name="email" required />
      </label>
      <button type="submit">Submit</button>
    </Form>
  );
};

export default ContactForm;
```

- The `Form` component **automatically** submits data to the associated route’s `action` function.
- Setting `method="post"` ensures that the form data is sent via **POST** instead of the default **GET**.


To access the result returned by the `action`, use the `useActionData` hook.

```JSX
import { Form, useActionData } from "react-router-dom";

const ContactForm = () => {
  const actionData = useActionData(); // Gets the response from the action

  return (
    <div>
      <Form method="post">
        <label>
          Name:
          <input type="text" name="name" required />
        </label>
        <label>
          Email:
          <input type="email" name="email" required />
        </label>
        <button type="submit">Submit</button>
      </Form>

      {actionData?.success && <p>Form submitted successfully!</p>}
    </div>
  );
};

export default ContactForm;
```


Example: Using `Form` with `GET`

```JSX
import { Form, useSearchParams } from "react-router-dom";

const SearchPage = () => {
  const [searchParams] = useSearchParams();
  const query = searchParams.get("query") || "";

  return (
    <div>
      <h1>Search Page</h1>
      <Form method="get">
        <input type="text" name="query" defaultValue={query} />
        <button type="submit">Search</button>
      </Form>

      <p>Searching for: {query}</p>
    </div>
  );
};
```

**Example:**
1. **Before Searching**
	🔗 URL: `/search`  
	🏷 UI Output:
	`Searching for:`
2. **User Inputs "React" and Clicks Search**
	🔗 URL Updates to: `/search?query=React`  
	🏷 UI Output:
	`Searching for: React`

### 🔑 Main Differences between Loader and Form with GET**

| Feature            | `loader` (Preload Data)                    | `Form` with `GET` (User Input)                                 |
| ------------------ | ------------------------------------------ | -------------------------------------------------------------- |
| **When it runs?**  | Before rendering                           | After form submission                                          |
| **Data fetching?** | Automatic                                  | User-driven                                                    |
| **Modifies URL?**  | No                                         | Yes (`?query=value`)                                           |
| **Ideal for?**     | Fetching required data before a page loads | Searching, filtering, and updating content based on user input |
| **Data access?**   | `useLoaderData()`                          | `useSearchParams()`                                            |


## Redirecting
#### 1. Redirect Using `Navigate` Component
The `Navigate` component is used inside JSX to redirect the user to a different page.
```JSX
import { Navigate } from "react-router-dom";

const Home = () => {
  const user = false; // Simulate authentication check

  if (!user) {
    return <Navigate to="/login" replace />;
  }

  return <h1>Welcome to Home Page</h1>;
};
```
- If `user` is `false`, React Router **immediately redirects** to `/login`.
- The `replace` prop ensures that the previous route is replaced, preventing the user from navigating back with the browser's back button.
#### 2. Redirect Using `useNavigate` hook
If you need to **redirect programmatically**, use the `useNavigate` hook.

```JSX
import { useNavigate } from "react-router-dom";

const Dashboard = () => {
  const navigate = useNavigate();

  const handleLogout = () => {
    // Perform logout logic...
    navigate("/login");
  };

  return (
    <div>
      <h1>Dashboard</h1>
      <button onClick={handleLogout}>Logout</button>
    </div>
  );
};
```

#### 3. Redirect After Form Submission (`action` in React Router)

If you’re using **forms with actions** (React Router v6.4+), you can redirect after a form submission.

```JSX
import { Form, redirect } from "react-router-dom";

export const action = async ({ request }) => {
  const formData = await request.formData();
  const username = formData.get("username");

  if (username) {
    return redirect(`/profile/${username}`);
  }

  return null;
};

const LoginForm = () => {
  return (
    <Form method="post">
      <input type="text" name="username" placeholder="Enter username" required />
      <button type="submit">Login</button>
    </Form>
  );
};

```

- The user submits the form.
- The `action` function runs and processes the form data.
- If a username is provided, `redirect(`/profile/${username}`)` **redirects the user** to their profile page.

## NavLink

The `NavLink` component works like a normal `<Link>`, but it adds an `"active"` class to the link when it matches the current URL.

```jsx 
import { NavLink } from "react-router-dom";

const Navbar = () => {
  return (
    <nav>
      <NavLink to="/" end>Home</NavLink>
      <NavLink to="/about">About</NavLink>
      <NavLink to="/contact">Contact</NavLink>
    </nav>
  );
};
```
## JSX Routes

And for our final trick, many folks prefer to configure their routes with JSX. You can do that with `createRoutesFromElements`. There is no functional difference between JSX or objects when configuring your routes, it's simply a stylistic preference.

```jsx
import {
  createRoutesFromElements,
  createBrowserRouter,
  Route,
} from "react-router-dom";

const router = createBrowserRouter(
  createRoutesFromElements(
    <Route
      path="/"
      element={<Root />}
      loader={rootLoader}
      action={rootAction}
      errorElement={<ErrorPage />}
    >
      <Route errorElement={<ErrorPage />}>
        <Route index element={<Index />} />
        <Route
          path="contacts/:contactId"
          element={<Contact />}
          loader={contactLoader}
          action={contactAction}
        />
        <Route
          path="contacts/:contactId/edit"
          element={<EditContact />}
          loader={contactLoader}
          action={editAction}
        />
        <Route
          path="contacts/:contactId/destroy"
          action={destroyAction}
        />
      </Route>
    </Route>
  )
);
```

Copy code to clipboard