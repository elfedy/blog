---
title: "Storing previous urls and doing friendly redirects in phoenix"
date: 2018-07-09
---

When we have an applcation with authorization/authentication functionality in place, there are some cases when we may want redirect a user to the url he was previously visiting before logging in. This article shows how use plugs to achieve that in Phoenix.

## Strategy
We are going to use the following strategy:

1. Always store the last url of a GET request in a session variable.

2. When a user signs up or logs in redirect him to that stored url.


## Storing url in a session
We want to store the url of every get request that is not related to creating a new user or a new session. For this example we will asume we have REST `user`, `session` and `password_reset` routes, such as the ones used by the [phauxth](https://github.com/riverrun/phauxth) library. In this case, we would like to avoid storing the following routes:

```
GET     /users/new           
GET     /sessions/new       
GET     /password_resets/new
GET     /password_resets/edit
```

For storing the url we are going to use session storage. Session storage only stores data until user closes the tab he is currently browsing. This makes the most sense for our use case as we do not want the user to log in and get redirected to a page he may have visited some days ago.

With all this in mind, we could write a `FriendlyRedirect` module that exposes `store_path_in_session/2` to act as a plug. In order to do that, the function needs to take a `%Plug.Conn{}` struct and an options variable as arguments, and return another `Plug.Conn{}` struct, to be passed to the next plug in the pipeline.

```elixir
defmodule MyApp.FriendlyRedirect do
  import Plug.Conn

  def store_path_in_session(conn, _) do
    # Get HTTP method and url from conn
    method = conn.method
    path = conn.request_path

    # If conditions apply store path in session, else return conn unmodified 
    case {method, storable?(path)} do
      {"GET", true} ->
        put_session(conn, :firendly_redirect_path, path)
  
      {_, _} ->
        conn
    end
  end

  defp storable?(path) do
    !(url =~ r/user|session|password_resets/)
  end
end
```

We can make the module available in the router by importing it in its corresponding function in `lib/my_app.ex`.

```elixir
def router do
 quote do
	 use Phoenix.Router
	 import Plug.Conn
	 import Phoenix.Controller
	 import MyApp.FriendlyRedirect
 end
end
```

We then call the `store_path_in_session/2` function as a plug in the `:browser` pipeline of our router.

```elixir
pipeline :browser do
  ...
  plug(:store_url_for_login_retargeting)
end
```

## Redirecting
Now that we are storing the url, we have to redirect to it when the signup is successful. Let's add a public method to our previously defined module that gets the stored path or gives a default if no stored path is found. 

```elixir
defmodule MyApp.FriendlyRedirect do
  ...
  def target_path(conn) do
    target_path = 
      get_session(conn, :login_retargeting_path) || 
      default_retargeting_path
  end

  defp default_retargeting_path do
    "/"
  end
end
```

Then on sign up/log in, we would redirect to that path inside the corresponding controller action:

```elixir
redirect conn, to: FriendlyRedirect.target_path(conn)
```
