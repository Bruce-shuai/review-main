相关文档：
[Testing Library](https://testing-library.com/)
[Jest](https://jestjs.io/)

> 注意，没有必要记住所有内容，需要的时候，去查就可以了。

```jsx
/* App.test.js */
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders learn react link", () => {
  render(<App />);
  const linkElement = screen.getByText(/learn react/i);
  // 检查是否有具备文本 learn react 的节点
  expect(linkElement).toBeInTheDocument();
});

test("renders 3 list items", () => {
  render(<App />);
  const listItems = screen.getAllByRole("listitem");
  // expect(listItems).toHaveLength(3);
  expect(listItems.length).toBe(3);
});
```

```jsx
/** App.js */
<h1 data-testid="myTestId">Hello</h1>
<span title="sum"></span>

/** App.test.js */
test("renders title", () => {
  render(<App />);
  const title = screen.getByTestId("mytestid");
  expect(title).toBeInTheDocument();
})
```

```jsx
/** Login.jsx */
const Login = () => {
  const [error, setError] = useState(false);
  return (
    <div className="container">
      <form>
        <input type="text" placeholder="username" />
        <input type="password" placeholder="password" />
        <button disabled={true}>Login</button>
        <span
          data-testid="error"
          style={{ visibility: error ? "visible" : "hidden" }}
        >
          Something went wrong!
        </span>
      </form>
    </div>
  );
};

/** Login.test.js */
test("username input should be rendered", () => {
  render(<Login />);
  const userInputEl = screen.getByPlaceholderText(/username/i);
  expect(userInputEl).toBeInTheDocument();
});

test("password input should be rendered", () => {
  render(<Login />);
  const passwordInputEl = screen.getByPlaceholderText(/password/i);
});

test("button should be rendered", () => {
  render(<Login />);
  const buttonEl = screen.getByRole("button");
  expect(buttonEl).toBeInTheDocument();
});

test("username input should be empty", () => {
  render(<Login />);
  const userInputEl = screen.getByPlaceholderText(/username/i);
  expect(userInputEl.value).toBe("");
});

test("button should be disabled", () => {
  render(<Login />);
  const buttonEl = screen.getByRole("button");
  expect(buttonEl).toBeDisabled();
});

test("error message should be disabled", () => {
  render(<Login />);
  const errorEl = screen.getByTestId("error");
  expect(errorEl).not.toBeVisible();
});
```
