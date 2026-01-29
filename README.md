# ⚛️ React 19: The Complete Guide (বাংলায়)

আসসালামু আলাইকুম! 👋 এই রিপোজিটরিতে **React 19**-এর নতুন এবং গুরুত্বপূর্ণ ফিচারগুলো নিয়ে বিস্তারিত আলোচনা করা হয়েছে। প্রতিটি ফিচার একদম সহজ বাংলায়, রিয়েল-লাইফ উদাহরণ এবং কোড স্নিপেটসহ ব্যাখ্যা করা হয়েছে, যাতে একজন নতুন ডেভেলপারও সহজে বুঝতে পারেন।

---

## 📑 সূচিপত্র

1. [React Compiler (Automatic Memoization)](https://www.google.com/search?q=%231-react-compiler-automatic-memoization)
2. [use() Hook](https://www.google.com/search?q=%232-use-hook)
3. ["use client" & "use server" Directives](https://www.google.com/search?q=%233-use-client--use-server-directives)
4. [Server Actions (Data Mutation)](https://www.google.com/search?q=%234-server-actions-data-mutation)
5. [Form Actions API (useFormState & useFormStatus)](https://www.google.com/search?q=%235-form-actions-api-useformstate--useformstatus)
6. [useOptimistic Hook](https://www.google.com/search?q=%236-useoptimistic-hook)
7. [ref as a Prop (No more forwardRef)](https://www.google.com/search?q=%237-ref-as-a-prop-no-more-forwardref)
8. [Context API Update (No more Provider)](https://www.google.com/search?q=%238-context-api-update-no-more-provider)
9. [Asset Loading & Document Metadata](https://www.google.com/search?q=%239-asset-loading--document-metadata)
10. [Web Components Support](https://www.google.com/search?q=%2310-web-components-support)

---

## 1. React Compiler (Automatic Memoization)

### 🔹 ফিচার পরিচিতি

React Compiler হলো একটি নতুন টুল যা বিল্ড টাইমে আপনার কোডকে অটোমেটিক্যালি অপ্টিমাইজ করে। এটি `useMemo`, `useCallback`, এবং `React.memo`-এর কাজ নিজে নিজেই করে দেয়।

### 🔹 কেন এই পরিবর্তন?

ম্যানুয়ালি মেমোইজেশন করা সময়সাপেক্ষ এবং কোডকে জটিল করে তোলে। কম্পাইলার এই ঝামেলা দূর করে ডেভেলপারদের "Business Logic"-এ ফোকাস করতে দেয়।

### 🔹 কোড তুলনা

**React 18 (ম্যানুয়াল):**

```jsx
const Counter = () => {
  const [count, setCount] = useState(0);

  const increment = useCallback(() => {
    setCount((c) => c + 1);
  }, []); // ম্যানুয়ালি useCallback ব্যবহার করতে হতো

  return <Button onClick={increment}>Count: {count}</Button>;
};

```

**React 19 (অটোমেটিক):**

```jsx
const Counter = () => {
  const [count, setCount] = useState(0);

  // কোনো হুক ছাড়াই এটি মেমোইজড থাকবে
  const increment = () => {
    setCount((c) => c + 1);
  };

  return <Button onClick={increment}>Count: {count}</Button>;
};

```

---

## 2. use() Hook

### 🔹 ফিচার পরিচিতি

`use()` একটি নতুন হুক যা দিয়ে আপনি **Promise** এবং **Context** এর ভ্যালু খুব সহজে পড়তে পারেন। এটি কন্ডিশনাল ব্লক (`if-else`) বা লুপের ভেতরেও ব্যবহার করা যায়।

### 🔹 কোড তুলনা (Data Fetching)

**React 18 (useEffect):**

```jsx
const User = () => {
  const [data, setData] = useState(null);
  useEffect(() => {
    fetch('/api/user').then(r => r.json()).then(setData);
  }, []);
  if (!data) return "Loading...";
  return <h1>{data.name}</h1>;
};

```

**React 19 (use):**

```jsx
import { use, Suspense } from "react";

const User = ({ userPromise }) => {
  const user = use(userPromise); // সরাসরি প্রমিজ পড়া হচ্ছে
  return <h1>{user.name}</h1>;
};

// Parent Component
<Suspense fallback="Loading...">
  <User userPromise={fetch('/api/user').then(r => r.json())} />
</Suspense>

```

---

## 3. "use client" & "use server" Directives

### 🔹 ফিচার পরিচিতি

* `"use client"`: ফাইলটি ব্রাউজারে রান করবে এবং এতে ইন্টারেক্টিভিটি (onClick, useState) থাকবে।
* `"use server"`: ফাংশনটি শুধুমাত্র সার্ভারে রান করবে (Database calls, API secrets)।

### 🔹 উদাহরণ

```jsx
// actions.js
"use server"; // এটি সার্ভারে এক্সিকিউট হবে

export async function saveData(formData) {
  await db.create(formData);
}

```

```jsx
// FormComponent.jsx
"use client"; // এটি ক্লায়েন্টে রান করবে
import { saveData } from "./actions";

export default function Form() {
  return <form action={saveData}>...</form>;
}

```

---

## 4. Server Actions (Data Mutation)

### 🔹 ফিচার পরিচিতি

আগে ফর্ম সাবমিট করার জন্য আমাদের আলাদা API Route (`/api/submit`) বানাতে হতো। এখন আমরা সরাসরি সার্ভার ফাংশন (`Server Action`) কল করতে পারি `<form action={...}>` এর মাধ্যমে।

### 🔹 কোড তুলনা

**React 18 (API Route):**

```jsx
const onSubmit = async (e) => {
  e.preventDefault();
  await fetch('/api/submit', { method: 'POST', body: ... });
};

```

**React 19 (Server Action):**

```jsx
// সরাসরি সার্ভার ফাংশন পাস করা যায়
<form action={myServerAction}>
  <input name="username" />
  <button type="submit">Save</button>
</form>

```

---

## 5. Form Actions API (useFormState & useFormStatus)

### 🔹 ফিচার পরিচিতি

* **`useFormState`**: সার্ভার অ্যাকশন থেকে আসা রেজাল্ট বা এরর মেসেজ আপডেট করে।
* **`useFormStatus`**: ফর্ম সাবমিট হওয়ার সময় লোডিং অবস্থা (pending state) জানায়।

### 🔹 উদাহরণ

```jsx
import { useFormStatus } from "react-dom";

function SubmitButton() {
  const { pending } = useFormStatus();
  return <button disabled={pending}>{pending ? "Saving..." : "Save"}</button>;
}

```

---

## 6. useOptimistic Hook

### 🔹 ফিচার পরিচিতি

সার্ভার রেসপন্সের জন্য অপেক্ষা না করে সাথে সাথে UI আপডেট করার জন্য এটি ব্যবহার করা হয়।

### 🔹 উদাহরণ

```jsx
import { useOptimistic } from "react";

function Chat({ messages }) {
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (state, newMessage) => [...state, { text: newMessage, sending: true }]
  );

  const sendMessage = async (formData) => {
    addOptimisticMessage(formData.get("message")); // ১. সাথে সাথে দেখাবে
    await serverAction(formData); // ২. ব্যাকগ্রাউন্ডে সার্ভারে পাঠাবে
  };

  return (
    <div>
      {optimisticMessages.map((m) => (
        <div>{m.text} {m.sending && "(Sending...)"}</div>
      ))}
      <form action={sendMessage}>...</form>
    </div>
  );
}

```

---

## 7. ref as a Prop (No more forwardRef)

### 🔹 ফিচার পরিচিতি

এখন ফাংশনাল কম্পোনেন্টে `ref` পাস করার জন্য `forwardRef` ব্যবহার করার প্রয়োজন নেই। `ref` এখন সাধারণ prop হিসেবে কাজ করে।

### 🔹 কোড তুলনা

**React 18:**

```jsx
const MyInput = forwardRef((props, ref) => {
  return <input ref={ref} {...props} />;
});

```

**React 19:**

```jsx
const MyInput = ({ ref, ...props }) => {
  return <input ref={ref} {...props} />;
};
// ব্যবহার: <MyInput ref={inputRef} />

```

---

## 8. Context API Update (No more Provider)

### 🔹 ফিচার পরিচিতি

এখন `<Context.Provider>` লেখার দরকার নেই, শুধু `<Context>` লিখলেই হবে।

**React 19:**

```jsx
const ThemeContext = createContext();

function App() {
  return (
    <ThemeContext value="dark">
      <Children />
    </ThemeContext>
  );
}

```

---

## 9. Asset Loading & Document Metadata

### 🔹 ফিচার পরিচিতি

React 19 এখন ইমেজ, ফন্ট বা স্টাইলশিট লোডিং নিজে ম্যানেজ করে। এছাড়া `<title>`, `<meta>` ট্যাগগুলো এখন কম্পোনেন্টের ভেতর থেকেই পরিবর্তন করা যায় (আগে `react-helmet` লাগত)।

### 🔹 উদাহরণ

```jsx
function BlogPost({ post }) {
  return (
    <article>
      {/* React অটোমেটিক এই ট্যাগগুলো <head>-এ নিয়ে যাবে */}
      <title>{post.title}</title>
      <meta name="description" content={post.summary} />
      
      {/* স্টাইল লোড না হওয়া পর্যন্ত সাসপেন্স কাজ করবে */}
      <link rel="stylesheet" href="blog-theme.css" precedence="default" />
      
      <h1>{post.title}</h1>
    </article>
  );
}

```

---

## 10. Web Components Support

### 🔹 ফিচার পরিচিতি

React এখন সম্পূর্ণভাবে **Web Components** সাপোর্ট করে। আগে কাস্টম এলিমেন্টের ইভেন্ট হ্যান্ডেল করা কঠিন ছিল, এখন এটি সাধারণ React কম্পোনেন্টের মতোই কাজ করে।

### 🔹 উদাহরণ

```jsx
function App() {
  return (
    // 'my-slider' একটি Web Component
    <my-slider
      value="50"
      onchange={(e) => console.log(e.target.value)} // ইভেন্ট এখন কাজ করে
      class="custom-class" // class vs className সমস্যা সমাধান
    />
  );
}

```

---

## ❤️ অবদান (Contribution)

এই রিপোজিটরিটি যদি আপনার উপকারে আসে, তবে একটি ⭐ স্টার দিতে ভুলবেন না! কোনো ভুল থাকলে বা নতুন কিছু যোগ করতে চাইলে নির্দ্বিধায় **Pull Request** করতে পারেন।

Happy Coding with React 19! 🚀
