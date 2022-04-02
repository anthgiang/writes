---
layout: post
title: Better codebases
tags: Writes
time: Apr 03, 2022
---

![bees]({{site.baseurl}}/assets/bees.jpg)

Working on a large existing codebase has taught me what's truly important when
maintaining one. As always, **many developers have strong opinions on topics
they don't know enough about**, and here are a few of my own. <sup>(On a JS
codebase)</sup>

<br />

#### 1. Make it make sense

> "What does this even do?" - myself.

Occasionally I come across blocks of code that are puzzling. And when code
doesn't make sense, it will eventually get refactored by someone else. Usually
it's not because of the framework, library, or function, as their
documentation/definitions can be looked up. It is normally because of over
abstraction and redirection.

I have encountered this simplified example before. What happens when the
"Submit" button is clicked?

```jsx
/* LoginForm.jsx */
const LoginForm = (props) => {
  const { handleSubmit } = props;
    ...
    ...
    ...
  return (
    <form onSubmit={handleSubmit}>
      {/* Form input */}
      <button type="submit">Submit</button>
    </form>
  );
};

export default LoginForm;

```

```jsx
/* Login.jsx */
import LoginForm from "./LoginForm";
import loginUserAction from "../actions/authActions";

class Login extends React.Component {
    ...
    ...
    ...
  submitValues = (values) => {
    const { loginUserAction } = this.props;
    loginUserAction(values);
  };

  render() {
    <>
        ...
      <LoginForm handleSubmit={this.submitValues} />
    </>;
  }
}

const mapDispatchToProps = {
  loginUserAction,
};

export default connect(mapStateToProps, mapDispatchToProps)(Login);
```

```jsx
/* authActions.js */
import actions from "./actions";

export const loginUserAction = (user) => ({
  type: actions.AUTH.LOGIN_USER,
  payload: user,
});

/* Then the typical Redux (Saga / Thunk) flow from here. */
```

Essentially, it leads you through a long series of jumps, hoops, and hurdles.
And finally after you've navigated through 6+ files and figured it out, you've
already forgotten why you needed to know to begin with. So therefore, I aim to
write with minimal redirections and abstractions to make it easier on others.

<br />

#### 2. Make it clean and tidy

Every developer has their own **_*unique*_** code style, but it would be very
helpful if a team all agreed on one convention. I personally use these:

- **Auto-format on file save with Prettier**

  It pains me when a file hasn't been formatted, as a format can introduce 100+
  diffs. This then makes the pull request painful to understand what actually
  changed. So it would be great if files were simply formatted to start with.

- **Code comment styles**

  I try to use these conventions:

  ```js
  const README = `// Line comments are temporary notes.
                  /* Block comments are permanent notes. */`;

  // TODO: Do this, because this.
  // NOTE: Note this, because this.
  // HACK: This is working, for now.
  // BUG: This isn't working.

  /* This component does this and this. */
  ```

- **Print statements**

  I try to remove all my `console.log()` statements after I've finished
  developing. But sometimes when they're left behind by others, they're a useful
  indicator of what variables to track when debugging...

- **ESlint disables**

  I like having `/* eslint-disable no-magic-numbers */` at the top for an entire
  file, rather than for every line with
  `// eslint-disable-next-line no-magic-numbers`. I find that having to do every
  line makes the code messy and distracts from reading the actual code.

  Also I find magic numbers completely fine, as a good comment can clear up
  ambiguity, like this:

  ```js
  setTimeout(() => console.log("Yes magic numbers.")), 5000 /* milliseconds */);
  ```

<br />

#### 3. Make it documented

Reusuable components should be documented, or at least listed, somewhere. I have
written a piece of logic before, only to find that there was already a component
already made. Ideally, API endpoints should also be documented.

Regarding commit messages, as long as they are English words, it's probably
fine, as the code is the source of truth. The main pieces of information from a
commit is the author and the date. The author, so I know who to ask if needed,
and the date, so I can make an educated guess if a code block is stale and might
need a refactor.

<br />

### Back to reality

Realistically though, deadlines must be met and updates need to be deployed, and
sometimes bad practices are simply unavoidable. But if we all do our little bit,
we can make our codebases a better place.
