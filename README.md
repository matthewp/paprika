# Paprika

A Clojure library for the [App.net API](http://developers.app.net). An [App.net Developer account](https://join.app.net/signup?plan=developer) is required to use this library.

## Usage

Paprika is available on [Clojars](https://clojars.org/com.literallysoftware/paprika). Add the following dependency to your `project.clj` in order to use it.

```clojure
[com.literallysoftware/paprika "0.0.2"]
```

### Getting Started

The first thing you need to do is obtain an access token. If you have a developer account, you can [generate one](https://account.app.net/developer/apps/) from one of your apps. You could also try using [lein-paprika](https://github.com/literally/lein-paprika) to generate one from Leiningen. If you don't have a developer account, you can use [Dev Lite](http://dev-lite.jonathonduerig.com/) to generate one.

Once you have a token, launch a REPL and store your token in a var so it's easy to refer to later.

```clojure
(def t "YOUR_TOKEN")
```

Then require `paprika.core`. Here the namespace is aliased as `adn`.

```clojure
(require '[paprika.core :as adn])
```

In `paprika.core` there is a function for every App.net endpoint for User and Post objects. The next few sections will walk you through using two of them to give an idea for how they map to the API.

Also, it is suggested that you wrap each of the function calls with `clojure.pprint/pprint` so it's easier to read the output.

### Looking Up a User

Each endpoint function follows a general input and output structure. Lets use `lookup-user` as our first example. It requires one argument, and that is one of three possible user identifiers. You can provide the user's ID, their @username as a string (including the @ symbol), or the string "me" for the currently authenticated user.

```clojure
;; Lookup the currently authenticated user.
(adn/lookup-user "me")

;; Lookup @literally
(adn/lookup-user "@literally")
;; or
(adn/lookup-user "29711")
```

Each of those calls will return the [User object](http://developers.app.net/docs/resources/user/) for that user by default. Specifically, it returns the value of the `data` key in the [response envelope](http://developers.app.net/docs/basics/responses/#response-envelope). If you want the entire response envelope returned, then you can specify that by passing in an options map as the last argument.

```clojure
;; Return the entire response envelope
(adn/lookup-user "me" {:return :envelope})
;;=> {:meta {...} :data {...}}

;; Return the entire response (for debugging)
(adn/lookup-user "me" {:return :response})
```

The default value for `:return` is `:data`. This option is specific to Paprika and is not part of the App.net API.

The options map is also where you would provide general parameters such as the pagination and annotation parameters. Each object also has a set of general query parameters. For example, the API allows you to not include the HTML version of the user's profile description.

```clojure
(adn/lookup-user "me" {:include-html 0})
```

You should notice that `:html` is no longer in map for the `:description`.

### Creating a Post

In order to create a post, you need to have an access token. You provide this as part of the options map. While an access token is not optional for creating a post, it is optional for other endpoints, so that is why it is in the options ma. (In case you skipped the first steps, the var `t` refers to your access token.)

```clojure
(adn/create-post "I am posting this from my #clojure repl!" {:access-token t})
```

For `create-post`, the first argument is the text of the post. If you want to specify that the post is a reply, you can pass in a map containing values for `:text` and `:reply-to`.

```clojure
;; Reply to a post by specifying the post ID of the parent post.
(adn/create-post {:text "Post!" :reply-to "POST_ID"} {:access-token t})
```

You may be wondering why `:reply-to` is not in the options map. This is because the first argument to `create-post` is really the body of the HTTP POST request. Being able to provide just the text is a special case only for this function.

## Support

* Search the [issues](/issues), and open one (or a pull request) if you didn't find your answer.
* Message [@literally](https://app.net/literally) or [@jeremyheiler](https://app.net/jeremyheiler) on App.net.
* Join the [Paprika](http://patter-app.net/room.html?channel=17641) Patter room.

## License

Copyright © 2013 Literally Software Inc.

Distributed under the Eclipse Public License, the same as Clojure.
