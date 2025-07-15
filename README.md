# Fitness Intro

Fitness Trackr is a platform where fitness enthusiasts can share their workouts and
discover new routines. Anyone can browse the site and make an account, and users with an
account will be able to upload and manage their own activities.

This information is accessible via [the Fitness Trackr API](https://fitnesstrac-kr.herokuapp.com/).

This activity will guide you through reading some code that has already been written. It
involves many of the techniques introduced previously, such as effects, context, and custom hooks.

> [!TIP]
>
> Remember that you can clone this repository down and run the code locally
> to help you answer the questions as you read the code!

## Navigating between different pages

The app will render different components depending on which page is active. This logic is
managed in `PageContext`.

1. When the app first loads, what page does it start on? --> Activities (PageContext.jsx line 6) 
2. What are the four possible pages of `<App>`? --> Register, Login, Activities, Error404
3. Notice that `<App>` is wrapped by `<Layout>` in `main.jsx`. What is the purpose of the
   `<Layout>` component? --> to provide struture/context for Navbar and App.jsx (adds header and puts children in the main block)
4. How does `<Navbar>` navigate users to different pages? --> has links directing to Register, Login, Activities
5. `<Navbar>` uses the presence of a token to tell whether the user is logged in or not.\
   Where is `<Navbar>` getting the token from? --> useAuth() provider for the login token and AuthContext.jsx
6. What does `<Navbar>` render if the user is logged in? -->if the user is logged in, the Navbar will render a logout and Activities link
7. What does `<Navbar>` render if the user is _not_ logged in? -->the Navbar will render a register, activities and login  link

## Handle user authentication

Users can register for a new account, or log into an existing account. This logic is
managed in `AuthContext`.

8. What state does `AuthContext` manage? What value does it provide? -->login token (initially set to undefined) and setToken 
9. Which API endpoint does the `register` function make a request to? -->"/users/register"
10. What happens if the request is successful? --> if the response is OK, POST will add/create data to the API + return the successful POST response (should include .token) (line 20)
11. How are the `register` and `login` functions different? -->one goes to register, the other goes to login (different API endpoint)
12. What does it mean for a user to log out? What state is changed? -->the state of the token is changed to null (line 36)

The `<Register>` and `<Login>` components use form actions to authenticate the user.

13. What page is the user redirected to after they successfully register for an account? -->redirects to activities page
14. What happens if there's an error with registration? -->provides an error meg (for instance: Password Too Short!) line 35 in Register.jsx
15. How are the `<Register>` and `<Login>` components different? -->different return values, including: login page redirecting to register page if user does not have an account; register page redirecting user to login page if user already has an account

## Handle API communication

The API will send back a token once the user successfully registers or logs in. We want to
attach that token to any future requests to the API so that the user can access protected
routes.

There's a lot happening, so read through this portion very carefully!

### useQuery and useMutation

The API logic is managed in `ApiContext`, but the rest of our app will mostly be
interacting with the API via two custom hooks: `useQuery` and `useMutation`.

16. Let's start with `useQuery`. What is the purpose of the `data` state variable? -->to store the result of the useQuery request 
17. What about `loading` and `error`? What do these state variables represent? --> loading: the query is still running and whether to do a loading screen (waiting the network to respond); error: initially null, line 19: console log error (network request failed)
18. What happens in the `finally` block of the `try..catch` statement? -->the finally block always executes, whether the exception is thrown or caught ; whether the catch ...block (error) is caught, always setLoading(false) 
https://www.w3schools.com/jsref/jsref_try_catch.asp 
19. How often does the `useEffect` run, and what does it do? -->line 28: [] empty => only runs once ; if tag is present, run the provideTag() function, if not: run the query() function
20. Where are `provideTag` and `request` coming from? --> useApi() in line 5 ; useApi() function from line 40 in ApiContext.jsx
21. What does the `useQuery` hook return? -->state variables -- data, loading, error 
22. Now take a look at `useMutation`.
    1. How are the parameters different from `useQuery`'s parameters? -->useQuery's parameters: resource, tag ; useMutation's parameters: method, resource, tagsToInvalidate)
    2. How is the API request different from `useQuery`'s request? -->useMutation: API request any ; useQuery: API request GET (default, as specified in ApiContext.jsx)
    3. What does `useMutation` return? -->mutate (function) , state variables data, loading, error 

### Reuse request logic

Both `useQuery` and `useMutation` make requests to the API; this shared logic is kept in
`ApiContext`.

23. How does `ApiContext` use the token from `AuthContext`?
24. `/json/` is an example of a **regular expression**, which are often used to _test_
    if a pattern can be found in a string. How is it used in `request`?
25. What does `request` return if the API call succeeds? What if it fails?
26. In summary, defining `request` in `ApiContext` allows `useQuery` and `useMutation`
    to share what logic and information?

### Refresh queries automatically with tags

**Tags** are a strategy used to ensure that our local application state stays up to date
with the data on the server. Each query can provide a tag, and each mutation can invalidate a list of tags,
which specify the queries that need to be refreshed after that mutation.

27. In `ApiContext`, what is the initial value of the `tags` state variable?
28. What does `useQuery` provide as arguments into `provideTag`?
29. How does `provideTag` change the `tags` state?
30. What argument does `useMutation` pass into `invalidateTags`?
31. How does `invalidateTags` use the `tags` state?
