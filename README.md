# React 19 এর নতুন Feature সমূহ: একটি সম্পূর্ণ গাইড

আসসালামু আলাইকুম! এই README ফাইলে আমরা React 19 এর নতুন এবং গুরুত্বপূর্ণ কয়েকটি ফিচার নিয়ে আলোচনা করব। প্রত্যেকটি ফিচার সহজ ভাষায় এবং উদাহরণ দিয়ে বোঝানোর চেষ্টা করা হয়েছে, যাতে একজন নতুন React ডেভেলপারও সহজে বুঝতে পারেন।

---

## ১. React Compiler (অটোমেটিক মেমোইজেশন)

### Feature কী?

React Compiler হলো একটি নতুন টুল যা আমাদের কোডকে স্বয়ংক্রিয়ভাবে অপ্টিমাইজ করে। এর প্রধান কাজ হলো, React কম্পোনেন্ট এবং হুকগুলোকে নিজে থেকেই `memoize` করা। ফলে, `useMemo`, `useCallback`, এবং `React.memo` ব্যবহার করার প্রয়োজন অনেকাংশে কমে যায়।

### কেন এই feature এসেছে?

আগে React-এ পারফরম্যান্স অপ্টিমাইজ করার জন্য আমাদের ম্যানুয়ালি `useMemo` এবং `useCallback` ব্যবহার করতে হতো। কোডবেস বড় হলে এটি বেশ জটিল এবং কষ্টসাধ্য হয়ে যেত। ডেভেলপারদের এই কষ্ট কমানোর জন্যই React Compiler এসেছে। এটি নিজে থেকেই অপ্রয়োজনীয় re-render হওয়া বন্ধ করে এবং অ্যাপ্লিকেশনকে দ্রুততর করে তোলে।

### আগে কীভাবে করা হতো?

আগে, একটি ফাংশন বা ভ্যালুকে re-render এর সময় মনে রাখার জন্য আমরা `useCallback` বা `useMemo` ব্যবহার করতাম। আপনার প্রজেক্টের `auto-memo/Counter.jsx` ফাইলে এর একটি উদাহরণ রয়েছে।

```jsx
// src/components/auto-memo/Counter.jsx

import { useCallback, useState } from "react";
import ShowCount from "./ShowCount";
import Button from "./Button";

const Counter = () => {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);

  const incrementByOne = useCallback(() => {
    setCount1((prevCount) => prevCount + 1);
  }, []);

  const incrementByFive = useCallback(() => {
    setCount2((prevCount) => prevCount + 5);
  }, []);

  return (
    <div>
      <h1 className="text-2xl font-bold mb-4">Counter</h1>
      <div className="space-y-4">
        <ShowCount count={count1} title="Counter 1" />
        <Button onClick={incrementByOne}>Increment by One</Button>
        <hr />
        <ShowCount count={count2} title="Counter 2" />
        <Button onClick={incrementByFive}>Increment by Five</Button>
      </div>
    </div>
  );
};

export default Counter;
```

এখানে `incrementByOne` এবং `incrementByFive` ফাংশনকে `useCallback` দিয়ে র‍্যাপ করা হয়েছে, যাতে `Counter` কম্পোনেন্ট re-render হলেও এই ফাংশনগুলো নতুন করে তৈরি না হয়।

### এখন React 19–এ কীভাবে হচ্ছে?

React 19 এর কম্পাইলার চালু থাকলে, আমাদের আর `useCallback` ব্যবহার করার প্রয়োজন নেই। কম্পাইলার নিজে থেকেই বুঝে নেয় কোন ফাংশনকে memoize করতে হবে।

```jsx
// React 19 এ কোডটি এমন হবে

import { useState } from "react";
import ShowCount from "./ShowCount";
import Button from "./Button";

const Counter = () => {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);

  // useCallback ছাড়াই ফাংশনগুলো অপ্টিমাইজ হয়ে যাবে
  const incrementByOne = () => {
    setCount1((prevCount) => prevCount + 1);
  };

  const incrementByFive = () => {
    setCount2((prevCount) => prevCount + 5);
  };

  return (
    // ... JSX অপরিবর্তিত থাকবে
  );
};
```

### Beginner notes / mental model

ভাবুন, আপনার একজন ব্যক্তিগত সহকারী আছে। আগে আপনাকে প্রত্যেকটি কাজ মনে রাখার জন্য তাকে আলাদা আলাদা করে বলতে হতো (যেমন `useCallback` ব্যবহার করা)। কিন্তু এখন আপনার সহকারী (React Compiler) এতটাই বুদ্ধিমান যে, সে নিজে থেকেই বুঝে নেয় কোন কাজটি মনে রাখতে হবে এবং কোনটি নয়। এতে আপনার কাজ অনেক সহজ হয়ে গেল।

---

## ২. use() হুক

### Feature কী?

`use()` একটি নতুন হুক যা দিয়ে আমরা promise বা context থেকে ডেটা পড়তে পারি। অন্যান্য হুক (যেমন `useState`, `useEffect`) এর মতো `use()` হুকটি কন্ডিশনাল ব্লক (if-else) বা লুপের ভেতরেও ব্যবহার করা যায়।

### কেন এই feature এসেছে?

আগে promise হ্যান্ডেল করার জন্য `useEffect` এবং `.then()` ব্যবহার করতে হতো, যা কোডকে জটিল করে তুলত। বিশেষ করে, ডেটা লোড হওয়ার সময় loading state দেখানো এবং error হ্যান্ডেল করা বেশ ঝামেলার ছিল। `use()` এই প্রক্রিয়াকে অনেক সহজ করে দিয়েছে।

### আগে কীভাবে করা হতো (Data Fetching)?

আপনার প্রজেক্টের `use-hook-data-fetching/Starter.jsx` ফাইলে আমরা দেখতে পাই, ডেটা ফেচ করার জন্য `useEffect` এবং `useState` ব্যবহার করা হয়েছে।

```jsx
// src/components/use-hook-data-fetching/Starter.jsx

import { useEffect, useState } from "react";

const Starter = () => {
  const [user, setUser] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        const res = await fetch("https://jsonplaceholder.typicode.com/users/1");
        const data = await res.json();
        setUser(data);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, []);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  // ...বাকি কোড
};
```

### এখন React 19–এ কীভাবে হচ্ছে (Data Fetching)?

`use()` হুক ব্যবহার করলে promise থেকে সরাসরি ডেটা পাওয়া যায়। React নিজে থেকেই `Suspense` ব্যবহার করে loading state পরিচালনা করে।

আপনার প্রজেক্টের `use-hook-data-fetching/Final.jsx` ফাইলে এর চমৎকার উদাহরণ আছে।

```jsx
// src/components/use-hook-data-fetching/Final.jsx

import { Suspense, use } from "react";

// একটি Promise যা ডেটা ফেচ করে
const fetchUser = async () => {
  const res = await fetch("https://jsonplaceholder.typicode.com/users/1");
  return res.json();
};

const UserProfile = () => {
  // use() হুক দিয়ে সরাসরি ডেটা পাওয়া যাচ্ছে
  const user = use(fetchUser());

  return (
    <div>
      <h2 className="text-xl font-semibold">{user.name}</h2>
      <p>Email: {user.email}</p>
      <p>Phone: {user.phone}</p>
    </div>
  );
};

const Final = () => {
  return (
    <div>
      <h1 className="text-2xl font-bold mb-4">User Profile</h1>
      {/* Suspense ডেটা লোড হওয়া পর্যন্ত fallback UI দেখায় */}
      <Suspense fallback={<div>Loading...</div>}>
        <UserProfile />
      </Suspense>
    </div>
  );
};
```

### আগে কীভাবে করা হতো (Context)?

`useContext` ব্যবহার করে আমরা context থেকে ডেটা নিতাম।

```jsx
// src/components/use-hook-context/Starter.jsx এর অংশ

import { createContext, useContext, useState } from "react";

const ThemeContext = createContext();

const ThemeProvider = ({ children }) => {
  // ...
};

const ThemedButton = () => {
  // useContext ব্যবহার করা হয়েছে
  const { theme, toggleTheme } = useContext(ThemeContext);
  // ...
};
```

### এখন React 19–এ কীভাবে হচ্ছে (Context)?

`useContext` এর পরিবর্তে `use(ThemeContext)` ব্যবহার করা যায়। এর বড় সুবিধা হলো, এটি কন্ডিশনের ভেতরেও কাজ করে।

```jsx
// src/components/use-hook-context/Final.jsx এর অংশ

import { createContext, use, useState } from "react";

// ... Context তৈরি করা

const ThemedButton = () => {
  // use() দিয়ে context এর value পাওয়া যাচ্ছে
  const { theme, toggleTheme } = use(ThemeContext);

  // ...
};
```

### Beginner notes / mental model

`use()` হুককে একটি জাদুর বাক্স হিসেবে ভাবুন। আপনি যদি তাকে একটি promise দেন, সে ডেটা না আসা পর্যন্ত অপেক্ষা করবে এবং ডেটা এলে আপনাকে সরাসরি দিয়ে দেবে। আপনাকে loading state নিয়ে ভাবতে হবে না। আবার যদি তাকে একটি context দেন, সে সেই context এর ভ্যালু আপনাকে এনে দেবে। এটি React হুকের একটি সুপারপাওয়ার, যা যেকোনো জায়গায় ব্যবহার করা যায়।

---

## ৩. "use client" এবং "use server"

### Feature কী?

`"use client"` এবং `"use server"` হলো দুটি ডিরেক্টিভ বা নির্দেশিকা।
*   `"use client"`: একটি ফাইলের শুরুতে এটি লিখলে, ওই ফাইলের কম্পোনেন্টগুলো ক্লায়েন্ট-সাইড কম্পোনেন্ট (Client Component) হিসেবে গণ্য হয়। অর্থাৎ, এগুলোর কোড ইউজারের ব্রাউজারে রান করে এবং ইন্টারেক্টিভ হতে পারে (যেমন `useState`, `useEffect` ব্যবহার করা)।
*   `"use server"`: কোনো ফাংশনের শুরুতে এটি লিখলে, ওই ফাংশনটি একটি সার্ভার অ্যাকশন (Server Action) হয়ে যায়। এর মানে হলো, ফাংশনটি শুধুমাত্র সার্ভারে রান করবে, ক্লায়েন্টে নয়।

### কেন এই feature এসেছে?

React Server Components (RSC) এর ধারণা থেকে এই ফিচারগুলো এসেছে। এর মূল উদ্দেশ্য হলো, কিছু কাজ সার্ভারে এবং কিছু কাজ ক্লায়েন্টে ভাগ করে দেওয়া। এতে 초기 লোডিং টাইম কমে এবং পারফরম্যান্স বাড়ে। যেমন, ডেটাবেস থেকে ডেটা আনার কাজটি সার্ভারে হওয়াই নিরাপদ এবং দ্রুত।

### এখন React 19–এ কীভাবে হচ্ছে?

আপনার প্রজেক্টের `action/Final.jsx` ফাইলে একটি Server Action এর উদাহরণ তৈরি করা আছে।

```jsx
// src/components/action/Final.jsx

import { useState } from "react";

// এই কম্পোনেন্টটি একটি ক্লায়েন্ট কম্পোনেন্ট
export default function Final() {
  const [name, setName] = useState("");
  const [result, setResult] = useState("");

  // এই server action টি form থেকে কল করা হবে
  // এটি সার্ভারে রান করবে
  const submitName = async (formData) => {
    "use server"; // <-- এই ডিরেক্টিভটি গুরুত্বপূর্ণ

    const name = formData.get("name");
    // সার্ভারে কিছু কাজ হচ্ছে, যেমন ডেটাবেসে সেভ করা
    const serverResult = `Hello, ${name}! This message is from the server.`;
    setResult(serverResult); // <- সরাসরি state আপডেট করা যায় না, এর জন্য useFormState লাগে
  };

  return (
    <div>
      <h1 className="text-2xl font-bold mb-4">Server Action Example</h1>
      <form action={submitName}>
        <input
          type="text"
          name="name"
          value={name}
          onChange={(e) => setName(e.target.value)}
          className="border p-2 rounded"
          placeholder="Enter your name"
        />
        <button type="submit" className="bg-blue-500 text-white p-2 rounded ml-2">
          Submit to Server
        </button>
      </form>
      {result && <p className="mt-4">{result}</p>}
    </div>
  );
}
```
*দ্রষ্টব্য: উপরের উদাহরণে `setResult` সরাসরি সার্ভার অ্যাকশন থেকে কাজ করবে না। সার্ভার থেকে state আপডেট করার জন্য `useFormState` ব্যবহার করতে হয়, যা আমরা পরের সেকশনে দেখব।*

### Beginner notes / mental model

আপনার অ্যাপকে একটি রেস্টুরেন্টের সাথে তুলনা করুন।
*   **সার্ভার (`"use server"`)**: এটা হলো রেস্টুরেন্টের রান্নাঘর। সব গুরুত্বপূর্ণ এবং গোপন কাজ (যেমন রান্না করা, রেসিপি গোপন রাখা) এখানেই হয়। বাইরের কেউ রান্নাঘরে ঢুকতে পারে না।
*   **ক্লায়েন্ট (`"use client"`)**: এটা হলো রেস্টুরেন্টের ডাইনিং এরিয়া। কাস্টমাররা এখানে বসে, ওয়েটারের সাথে কথা বলে (ইন্টারেকশন) এবং খাবার উপভোগ করে।

React 19 আপনাকে ঠিক করতে দেয়, কোন কাজটি রান্নাঘরে হবে এবং কোনটি ডাইনিং এরিয়ায়।

---

## ৪. Form Actions API (useFormStatus, useFormState)

### Feature কী?

*   `useFormState`: এটি একটি হুক যা সার্ভার অ্যাকশনের সাথে ফর্মের state পরিচালনা করতে সাহায্য করে। অ্যাকশন সফল হয়েছে নাকি ব্যর্থ, সেই অনুযায়ী UI আপডেট করা যায়।
*   `useFormStatus`: এই হুকটি `<form>` এর ভেতরের কোনো কম্পোনেন্ট থেকে ফর্মের স্ট্যাটাস (যেমন, সাবমিট হচ্ছে কিনা) জানতে পারে।

### কেন এই feature এসেছে?

আগে ফর্ম সাবমিট করার সময় loading state দেখানো এবং সার্ভার থেকে error message দেখানো বেশ জটিল ছিল। ডেভেলপারদের `useState` দিয়ে ম্যানুয়ালি এসব পরিচালনা করতে হতো। এই নতুন হুকগুলো ফর্ম ম্যানেজমেন্টকে অনেক সহজ এবং ডিক্লেয়ারেটিভ করে তুলেছে।

### আগে কীভাবে করা হতো?

আপনার প্রজেক্টের `useFormState/Starter.jsx` ফাইলে দেখানো হয়েছে, কীভাবে `useState` দিয়ে state পরিচালনা করা হতো।

```jsx
// src/components/useFormState/Starter.jsx

import { useState } from "react";

const Starter = () => {
  const [message, setMessage] = useState("");
  const [error, setError] = useState("");

  const addToCart = async (formData) => {
    const itemName = formData.get("itemName");
    if (itemName === "gadget") {
      setMessage("Added to cart!");
      setError("");
    } else {
      setError("Invalid item.");
      setMessage("");
    }
  };

  return (
    <div>
      {/* ... JSX কোড ... */}
      <form action={addToCart}>
        <input type="text" name="itemName" className="border p-2 rounded" />
        <button type="submit">Add to Cart</button>
      </form>
      {message && <p>{message}</p>}
      {error && <p>{error}</p>}
    </div>
  );
};
```

### এখন React 19–এ কীভাবে হচ্ছে?

`useFormState` এবং `useFormStatus` ব্যবহার করে কোডটি অনেক ক্লিনার হয়ে যায়। আপনার প্রজেক্টের `useFormState/Final.jsx` এবং `useFormStatus/Final.jsx` এ এর উদাহরণ আছে।

**`useFormState` উদাহরণ:**
```jsx
// src/components/useFormState/Final.jsx

import { useFormState } from "react-dom"; // react-dom থেকে ইম্পোর্ট করা হয়

const addToCart = (prevState, formData) => {
  const itemName = formData.get("itemName");
  if (itemName === "gadget") {
    return { message: "Added to cart!", error: null };
  } else {
    return { message: null, error: "Invalid item." };
  }
};

const Final = () => {
  const [state, formAction] = useFormState(addToCart, { message: null, error: null });

  return (
    <div>
      {/* ... JSX ... */}
      <form action={formAction}>
        <input type="text" name="itemName" className="border p-2 rounded" />
        <button type="submit">Add to Cart</button>
      </form>
      {state.message && <p>{state.message}</p>}
      {state.error && <p>{state.error}</p>}
    </div>
  );
};
```

**`useFormStatus` উদাহরণ:**
`useFormStatus` সাবমিট বাটনের লোডিং স্টেট দেখানোর জন্য খুব উপকারী।

```jsx
// src/components/useFormStatus/Final.jsx থেকে একটি অংশ

import { useFormStatus } from "react-dom";

function SubmitButton() {
  const { pending } = useFormStatus(); // এই হুকটি ফর্মের স্ট্যাটাস দেয়

  return (
    <button type="submit" disabled={pending}>
      {pending ? "Submitting..." : "Submit"}
    </button>
  );
}

// মূল কম্পোনেন্টে <form> এর ভেতরে <SubmitButton /> ব্যবহার করতে হবে।
// <form action={...}>
//   <SubmitButton />
// </form>
```

### Beginner notes / mental model

`useFormState` কে একজন ম্যানেজারের মতো ভাবুন, যে আপনার ফর্মের সব খবর রাখে। ফর্ম সাবমিট হলে সে সার্ভারের সাথে কথা বলে এবং ফলাফল (সফল না ব্যর্থ) আপনাকে জানায়।

`useFormStatus` হলো একজন রিপোর্টার যে ফর্মের ভেতরে থেকে লাইভ আপডেট দেয়। সে আপনাকে বলতে পারে, "ফর্ম এখন সাবমিট হচ্ছে, একটু অপেক্ষা করুন!"।

---

## ৫. useOptimistic হুক

### Feature কী?

`useOptimistic` একটি হুক যা দিয়ে UI-তে "আশাবাদী" বা অপটিমিস্টিক আপডেট দেখানো যায়। এর মানে হলো, সার্ভারের রেসপন্সের জন্য অপেক্ষা না করেই UI-তে পরিবর্তন দেখানো হয়। যদি সার্ভার থেকে কোনো error আসে, তাহলে UI আবার আগের অবস্থায় ফিরে যায়।

### কেন এই feature এসেছে?

ইউজার এক্সপেরিয়েন্স ভালো করার জন্য এই ফিচারটি খুব গুরুত্বপূর্ণ। যেমন, একটি লাইক বাটনে ক্লিক করার সাথে সাথেই যদি লাইক কাউন্ট বেড়ে যায়, ইউজারের কাছে মনে হয় অ্যাপটি খুব ফাস্ট। সার্ভারের জন্য অপেক্ষা করতে হলে একটি ল্যাগ বা ডিলে महसूस হতো।

### আগে কীভাবে করা হতো?

আগে এটি ম্যানুয়ালি করতে হতো। প্রথমে একটি state আপডেট করে UI পরিবর্তন করা হতো, তারপর সার্ভারে রিকোয়েস্ট পাঠানো হতো। রিকোয়েস্ট ফেইল করলে state আবার আগের অবস্থায় ফিরিয়ে আনা হতো। এই প্রক্রিয়াটি বেশ জটিল ছিল।

### এখন React 19–এ কীভাবে হচ্ছে?

আপনার প্রজেক্টের `useOptimistic/Final.jsx` ফাইলে এর একটি সুন্দর উদাহরণ রয়েছে।

```jsx
// src/components/useOptimistic/Final.jsx

import { useOptimistic, useState, useRef } from "react";

// ... message list ...

const Final = () => {
  const [messages, setMessages] = useState(initialMessages);
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (state, newMessage) => [...state, { text: newMessage, sending: true }]
  );
  const formRef = useRef(null);

  const formAction = async (formData) => {
    const message = formData.get("message");
    addOptimisticMessage(message); // অপটিমিস্টিক আপডেট
    formRef.current.reset();

    // সার্ভারে পাঠানোর অনুকরণ
    await new Promise((resolve) => setTimeout(resolve, 1000));
    
    // আসল state আপডেট
    setMessages((prev) => [...prev, { text: message }]);
  };

  return (
    <div>
      {/* ... */}
      {optimisticMessages.map((msg, index) => (
        <div key={index}>
          {msg.text}
          {msg.sending && <small> (Sending...)</small>}
        </div>
      ))}

      <form action={formAction} ref={formRef}>
        <input type="text" name="message" />
        <button type="submit">Send</button>
      </form>
    </div>
  );
};
```
এখানে, `addOptimisticMessage` কল করার সাথে সাথেই UI-তে "Sending..." সহ নতুন মেসেজটি দেখানো হয়। এক সেকেন্ড পর যখন আসল `setMessages` কল হয়, তখন "Sending..." লেখাটি চলে যায়।

### Beginner notes / mental model

মনে করুন আপনি একটি চিঠি পোস্ট করছেন। `useOptimistic` ব্যবহার করা মানে হলো, চিঠিটি পোস্ট করার সাথে সাথেই আপনি ধরে নিচ্ছেন যে প্রাপক এটি পেয়ে গেছে এবং আপনি আপনার ডায়েরিতে তা লিখে রাখলেন। যদি পরে জানতে পারেন চিঠি পৌঁছায়নি, তখন ডায়েরি থেকে লেখাটি কেটে দেবেন। এই "আগেই ধরে নেওয়া" ব্যাপারটিই হলো অপটিমিস্টিক আপডেট।

---

## ৬. `forwardRef` এর প্রয়োজনীয়তা হ্রাস

### Feature কী?

React 19-এ, `ref` কে একটি সাধারণ prop হিসেবে ফাংশনাল কম্পোনেন্টে পাস করা যায়। এর জন্য আর `forwardRef` HOC (Higher-Order Component) ব্যবহার করার প্রয়োজন নেই।

### কেন এই feature এসেছে?

`forwardRef` এর সিনট্যাক্স কিছুটা জটিল এবং নতুনদের জন্য বোঝা কঠিন ছিল। `ref`-কে একটি সাধারণ prop হিসেবে ব্যবহার করার সুযোগ দিয়ে React টিম কোডকে আরও সহজ এবং বোধগম্য করে তুলেছে।

### আগে কীভাবে করা হতো?

আপনার প্রজেক্টের `forwardRef/ForwardRef.jsx` ফাইলে `forwardRef` ব্যবহারের উদাহরণ দেওয়া আছে।

```jsx
// src/components/forwardRef/ForwardRef.jsx

import { forwardRef } from "react";

const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} className="border p-2 rounded" />;
});

// ব্যবহারের সময়
// const inputRef = useRef();
// <MyInput ref={inputRef} />
```

### এখন React 19–এ কীভাবে হচ্ছে?

এখন `ref` কে সরাসরি props থেকে পাওয়া যায়।

```jsx
// React 19 এ কোডটি এমন হবে

// forwardRef ছাড়াই ref prop হিসেবে পাওয়া যায়
const MyInput = ({ ref, ...props }) => {
  return <input {...props} ref={ref} className="border p-2 rounded" />;
};

// অথবা সরাসরি
// const MyInput = (props) => {
//   return <input {...props} className="border p-2 rounded" />;
// };

// ব্যবহারের নিয়ম একই থাকবে
// const inputRef = useRef();
// <MyInput ref={inputRef} />
```

### Beginner notes / mental model

আগে, একটি বাচ্চার হাতে তার বাবার কোনো জিনিস দিতে হলে, আপনাকে একজন মধ্যস্থতাকারী (`forwardRef`) দিয়ে পাঠাতে হতো। এখন, আপনি সরাসরি বাচ্চার হাতেই জিনিসটি (`ref`) দিয়ে দিতে পারছেন। প্রক্রিয়াটি এখন অনেক সহজ এবং সরাসরি।

---

## ৭. Asset লোডিং-এ উন্নতি

### Feature কী?

React 19-এ, `<link>`, `<script>`, `<style>` ইত্যাদি অ্যাসেট (asset) লোড হওয়ার প্রক্রিয়াটি অনেক উন্নত হয়েছে। React এখন Suspense ব্যবহার করে অ্যাসেট লোড হওয়া পর্যন্ত অপেক্ষা করে এবং পুরোপুরি লোড হয়ে গেলে তবেই UI রেন্ডার করে।

### কেন এই feature এসেছে?

আগে CSS বা ফন্ট লোড হওয়ার আগে UI দেখানো হলে একটি ফ্ল্যাশ (flickering) দেখা যেত, যাকে FOUC (Flash of Unstyled Content) বলা হয়। নতুন এই পদ্ধতিতে React নিশ্চিত করে যে, স্টাইল এবং অন্যান্য অ্যাসেটসহ সম্পূর্ণ UI একসাথে দেখানো হবে। এতে ইউজার এক্সপেরিয়েন্স মসৃণ হয়।

### প্রজেক্টে উদাহরণ না থাকলে:

এই উদাহরণটি ব্যাখ্যার জন্য নতুন করে তৈরি করা।

```jsx
// React 19 এ অ্যাসেট লোডিং

function MyComponent() {
  return (
    <div>
      {/* এই স্টাইলশীট লোড হওয়া পর্যন্ত React অপেক্ষা করবে */}
      <link rel="stylesheet" href="/my-stylesheet.css" precedence="default" />
      <h1 className="my-class-from-stylesheet">Styled Heading</h1>
      <p>This component will only render after the stylesheet has loaded.</p>
    </div>
  );
}

function App() {
  return (
    <Suspense fallback={<p>Loading assets...</p>}>
      <MyComponent />
    </Suspense>
  );
}
```
`precedence` অ্যাট্রিবিউট React-কে বলে দেয় কোন স্টাইলশীট আগে লোড করতে হবে।

### Beginner notes / mental model

ধরুন আপনি একটি মঞ্চে নাটক পরিবেশন করতে যাচ্ছেন। আগে, অভিনেতারা তৈরি হোক বা না হোক, পর্দা উঠে যেত, ফলে দর্শকরা অপ্রস্তুত অভিনেতাদের দেখে ফেলত। এখন React 19 নিশ্চিত করে যে, সব অভিনেতা (অ্যাসেট) মেকআপ নিয়ে প্রস্তুত হওয়ার পরেই পর্দা উঠবে। এতে দর্শকরা শুরু থেকেই একটি নিখুঁত পারফরম্যান্স দেখতে পায়।

---

## ৮. Web Component এর সাথে উন্নত সামঞ্জস্য

### Feature কী?

React 19 এখন Web Components (বা Custom Elements) এর সাথে অনেক ভালোভাবে কাজ করে। এটি এখন Web Components এর প্রপার্টি এবং ইভেন্টগুলোকে আরও সহজে চিনতে এবং পরিচালনা করতে পারে।

### কেন এই feature এসেছে?

অনেক সময় React অ্যাপ্লিকেশনে অন্য লাইব্রেরি দিয়ে তৈরি Web Components ব্যবহার করার প্রয়োজন হয়। আগে React এবং Web Components এর মধ্যে ডেটা পাস করা বা ইভেন্ট শোনা কিছুটা কঠিন ছিল। React 19 এই ইন্টিগ্রেশনকে আরও সহজ করে দিয়েছে।

### প্রজেক্টে উদাহরণ না থাকলে:

এই উদাহরণটি ব্যাখ্যার জন্য নতুন করে তৈরি করা।

ধরুন, আমাদের একটি Web Component আছে যার নাম `<my-custom-slider>`।

```javascript
// web-component.js
class MyCustomSlider extends HTMLElement {
  // ... component logic
}
customElements.define('my-custom-slider', MyCustomSlider);
```

**আগে React-এ যেভাবে ব্যবহার করতে হতো:**
```jsx
function App() {
  const sliderRef = useRef(null);

  useEffect(() => {
    // ম্যানুয়ালি property সেট করতে হতো
    sliderRef.current.value = 50; 
    // ম্যানুয়ালি event listener যোগ করতে হতো
    const handleChange = (e) => console.log(e.detail.value);
    sliderRef.current.addEventListener('value-changed', handleChange);

    return () => {
      sliderRef.current.removeEventListener('value-changed', handleChange);
    };
  }, []);

  return <my-custom-slider ref={sliderRef}></my-custom-slider>;
}
```

**এখন React 19-এ যেভাবে করা যায়:**
```jsx
function App() {
  const [sliderValue, setSliderValue] = useState(50);

  return (
    <my-custom-slider 
      value={sliderValue} // property সরাসরি prop হিসেবে পাস করা যায়
      onvalue-changed={(e) => setSliderValue(e.detail.value)} // event সরাসরি on-event prop হিসেবে ব্যবহার করা যায়
    >
    </my-custom-slider>
  );
}
```
এখন React নিজে থেকেই property এবং event গুলোকে সঠিকভাবে পরিচালনা করতে পারে।

### Beginner notes / mental model

Web Component-কে একটি বিদেশি বন্ধুর মতো ভাবুন যে অন্য ভাষায় কথা বলে। আগে, তার সাথে কথা বলতে আপনার একজন দোভাষীর সাহায্য লাগত। React 19 এখন নিজেই সেই ভাষা শিখে নিয়েছে, তাই আপনি সরাসরি তার সাথে কথা বলতে পারছেন।

---

## ৯. `React.lazy()` এর প্রয়োজনীয়তা হ্রাস

### Feature কী?

React Server Components (RSC) এর আর্কিটেকচারে, ক্লায়েন্ট কম্পোনেন্টগুলো ডিফল্টভাবেই কোড-স্প্লিটিং (code-splitting) এর সুবিধা পায়। এর ফলে, ক্লায়েন্ট-সাইডে ম্যানুয়ালি `React.lazy()` ব্যবহার করে কোড বিভক্ত করার প্রয়োজন কমে গেছে।

### কেন এই feature এসেছে?

`React.lazy` ক্লায়েন্ট-সাইড কোড-স্প্লিটিং এর জন্য একটি চমৎকার টুল ছিল, কিন্তু এটি সেটআপ করা কিছুটা ম্যানুয়াল কাজ। RSC মডেলে, সার্ভার শুধুমাত্র প্রয়োজনীয় কম্পোনেন্টের HTML পাঠায় এবং ক্লায়েন্ট-সাইড জাভাস্ক্রিপ্ট শুধুমাত্র তখনই লোড হয় যখন কোনো কম্পোনেন্ট ইন্টারেক্টিভ হওয়ার দরকার পড়ে। এই প্রক্রিয়াটি স্বয়ংক্রিয়ভাবে কোড-স্প্লিটিং করে, যা `React.lazy` এর কাজটিকে আরও সহজ করে দেয়।

### আগে কীভাবে করা হতো?

```jsx
import React, { Suspense, lazy } from 'react';

// lazy ব্যবহার করে একটি কম্পোনেন্ট ইম্পোর্ট করা হচ্ছে
const HeavyComponent = lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <div>
      <h1>My App</h1>
      <Suspense fallback={<div>Loading...</div>}>
        <HeavyComponent />
      </Suspense>
    </div>
  );
}
```

### এখন React 19–এ কীভাবে হচ্ছে?

Server Components এর সাথে, একটি ক্লায়েন্ট কম্পোনেন্ট (`"use client"` ব্যবহার করে) স্বাভাবিকভাবে ইম্পোর্ট করলেই React এবং এর বান্ডলার (যেমন Next.js বা Vite) স্বয়ংক্রিয়ভাবে কোড-স্প্লিটিং পরিচালনা করে।

```jsx
// App.jsx (Server Component)
import HeavyClientComponent from './HeavyClientComponent'; // এটি একটি ক্লায়েন্ট কম্পোনেন্ট

function App() {
  return (
    <div>
      <h1>My App</h1>
      {/* HeavyClientComponent এর কোড শুধুমাত্র ব্রাউজারে রেন্ডার হওয়ার সময় লোড হবে */}
      <HeavyClientComponent />
    </div>
  );
}

// HeavyClientComponent.jsx
"use client";

import { useState } from 'react';

export default function HeavyClientComponent() {
  // ... component logic
  return <div>This is a heavy client component.</div>;
}
```
এই মডেলে, `HeavyClientComponent` এর জাভাস্ক্রিপ্ট বান্ডেলটি মূল পেজ লোডের সাথে আসবে না। এটি প্রয়োজন অনুযায়ী পরে লোড হবে।

### Beginner notes / mental model

আপনার বাড়িতে একটি লাইব্রেরি আছে। আগে, ভারী বইগুলো (`HeavyComponent`) পড়ার জন্য আপনাকে আলাদা করে একটি ছোট টেবিলে (`Suspense`) আনতে হতো। এখন আপনার বাড়িটি (`Server Component`) এমনভাবে ডিজাইন করা যে, আপনি যখন যে বই পড়তে চান, সেই বইটিই কেবল আপনার সামনে আসে। বাকি বইগুলো শেলফেই গোছানো থাকে। এতে আপনার পড়ার টেবিল অগোছালো হয় না।

---

## ১০. `<Context.Provider>` এর পরিবর্তে `<Context>`

### Feature কী?

React 19-এ, Context এর value দেওয়ার জন্য আর `<MyContext.Provider>` ব্যবহার করতে হবে না। এখন সরাসরি `<MyContext>` কম্পোনেন্টকেই Provider হিসেবে ব্যবহার করা যায়।

### কেন এই feature এসেছে?

এটি একটি সিনট্যাকটিক সুগার (syntactic sugar), যা কোডকে আরও সংক্ষিপ্ত এবং পড়তে সহজ করে। `.Provider` অংশটি বাদ দেওয়ায় কোড দেখতে আরও ক্লিনার লাগে।

### আগে কীভাবে করা হতো?

আপনার প্রজেক্টের `use-hook-context/Starter.jsx` ফাইলে এর পুরনো পদ্ধতিটি দেখা যাচ্ছে।

```jsx
// src/components/use-hook-context/Starter.jsx

import { createContext, useContext, useState } from "react";

const ThemeContext = createContext();

const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light");
  const toggleTheme = () => { /* ... */ };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};
```

### এখন React 19–এ কীভাবে হচ্ছে?

এখন `.Provider` ছাড়াই Context ব্যবহার করা যায়।

```jsx
// src/components/use-hook-context/Final.jsx এর উন্নত সংস্করণ

import { createContext, use, useState } from "react";

const ThemeContext = createContext();

const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light");
  const toggleTheme = () => { /* ... */ };
  
  // .Provider ছাড়াই সরাসরি Context কম্পোনেন্ট ব্যবহার করা হচ্ছে
  return (
    <ThemeContext value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext>
  );
};
```

### Beginner notes / mental model

আগে Context কে একটি বিশেষ বাক্সের (`Provider`) ভেতরে করে পাঠাতে হতো। এখন, Context নিজেই একটি স্মার্ট বাক্স হয়ে গেছে, যার ভেতরে আপনি সরাসরি আপনার ডেটা (`value`) রেখে পাঠাতে পারেন। এটি শুধু একটি ছোট পরিবর্তন, কিন্তু কোডকে দেখতে সুন্দর করে।