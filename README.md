# 🚀 React 19: নতুন ফিচারের সহজ গাইড (কোডসহ)

আসসালামু আলাইকুম! 👋 আপনি যদি React শিখছেন বা React 19 এর নতুন ফিচারগুলো সহজে বুঝতে চান, তবে এই গাইডটি আপনার জন্য। এখানে কঠিন সংজ্ঞার বদলে **বাস্তব উদাহরণ** এবং **কোড** দিয়ে সব বোঝানো হয়েছে।

---

## 📑 সূচিপত্র

1. [React Compiler (স্মার্ট অ্যাসিস্ট্যান্ট)](https://www.google.com/search?q=%231-react-compiler-%E0%A6%B8%E0%A7%8D%E0%A6%AE%E0%A6%BE%E0%A6%B0%E0%A7%8D%E0%A6%9F-%E0%A6%85%E0%A7%8D%E0%A6%AF%E0%A6%BE%E0%A6%B8%E0%A6%BF%E0%A6%B8%E0%A7%8D%E0%A6%9F%E0%A7%8D%E0%A6%AF%E0%A6%BE%E0%A6%A8%E0%A7%8D%E0%A6%9F)
2. [use() Hook (জাদুর চাবি)](https://www.google.com/search?q=%232-use-hook-%E0%A6%9C%E0%A6%BE%E0%A6%A6%E0%A7%81%E0%A6%B0-%E0%A6%9A%E0%A6%BE%E0%A6%AC%E0%A6%BF)
3. [Server Actions (রান্নাঘর ও ডাইনিং)](https://www.google.com/search?q=%233-server-actions-%E0%A6%B0%E0%A6%BE%E0%A6%A8%E0%A7%8D%E0%A6%A8%E0%A6%BE%E0%A6%98%E0%A6%B0-%E0%A6%93-%E0%A6%A1%E0%A6%BE%E0%A6%87%E0%A6%A8%E0%A6%BF%E0%A6%82)
4. [Form Actions (অটোমেটিক ম্যানেজার)](https://www.google.com/search?q=%234-form-actions-%E0%A6%85%E0%A6%9F%E0%A7%8B%E0%A6%AE%E0%A7%87%E0%A6%9F%E0%A6%BF%E0%A6%95-%E0%A6%AE%E0%A7%8D%E0%A6%AF%E0%A6%BE%E0%A6%A8%E0%A7%87%E0%A6%9C%E0%A6%BE%E0%A6%B0)
5. [useOptimistic (আশাবাদী হওয়া)](https://www.google.com/search?q=%235-useoptimistic-%E0%A6%86%E0%A6%B6%E0%A6%BE%E0%A6%AC%E0%A6%BE%E0%A6%A6%E0%A7%80-%E0%A6%B9%E0%A6%93%E0%A7%9F%E0%A6%BE)
6. [No more forwardRef (মিডলম্যান বিদায়)](https://www.google.com/search?q=%236-no-more-forwardref-%E0%A6%AE%E0%A6%BF%E0%A6%A1%E0%A6%B2%E0%A6%AE%E0%A7%8D%E0%A6%AF%E0%A6%BE%E0%A6%A8-%E0%A6%AC%E0%A6%BF%E0%A6%A6%E0%A6%BE%E0%A7%9F)
7. [Context API (সহজ উপহার)](https://www.google.com/search?q=%237-context-api-%E0%A6%B8%E0%A6%B9%E0%A6%9C-%E0%A6%89%E0%A6%AA%E0%A6%B9%E0%A6%BE%E0%A6%B0)

---

## 1. React Compiler (স্মার্ট অ্যাসিস্ট্যান্ট)

### 🤔 সমস্যা কী ছিল?

আগে আমাদের বারবার বলে দিতে হতো—"এই ফাংশনটা মনে রেখো" (`useCallback`), "এই ক্যালকুলেশনটা মনে রেখো" (`useMemo`)। না হলে অ্যাপ স্লো হয়ে যেত। নতুনদের জন্য এটা বোঝা খুব কঠিন ছিল।

### 💡 React 19 কী করল?

এখন React এর নিজের একটি **"স্মার্ট কম্পাইলার"** আছে। সে নিজেই বোঝে কোনটা মনে রাখা দরকার। আপনাকে আর কষ্ট করে `useCallback` বা `useMemo` লিখতে হবে না।

**কোড তুলনা:**

❌ **আগে (React 18):**

```jsx
const increment = useCallback(() => {
  setCount((c) => c + 1);
}, []); // এই dependency array মনে রাখা ছিল কষ্টের!

```

✅ **এখন (React 19):**

```jsx
// সাধারণ ফাংশন লিখলেই হবে, কম্পাইলার বাকিটা সামলে নেবে!
const increment = () => {
  setCount((c) => c + 1);
};

```

---

## 2. use() Hook (জাদুর চাবি)

### 🤔 সমস্যা কী ছিল?

ডাটা ফেচ করার জন্য `useEffect` ব্যবহার করতে হতো। লোডিং দেখানো, এরর হ্যান্ডেল করা—অনেক কোড লিখতে হতো। আবার `if-else` এর ভেতরে হুক ব্যবহার করা যেত না।

### 💡 React 19 কী করল?

`use()` নামে একটা নতুন হুক এসেছে। এটি দিয়ে আপনি সরাসরি ডাটা (Promise) বা Context পড়তে পারবেন।

**কোড তুলনা:**

❌ **আগে (useEffect):**

```jsx
useEffect(() => {
  fetch('/api/user')
    .then(res => res.json())
    .then(data => setUser(data));
}, []);

```

✅ **এখন (use Hook):**

```jsx
import { use, Suspense } from "react";

const UserProfile = ({ userPromise }) => {
  // if (true) { ... } // চাইলে কন্ডিশনের ভেতরেও ব্যবহার করা যাবে!
  const user = use(userPromise); 
  return <h1>{user.name}</h1>;
};

// ব্যবহারের সময়
<Suspense fallback="Loading...">
  <UserProfile userPromise={fetchUser()} />
</Suspense>

```

---

## 3. Server Actions (রান্নাঘর ও ডাইনিং)

### 🤔 সমস্যা কী ছিল?

আগে ফর্ম সাবমিট করতে হলে আলাদা API রুট বানাতে হতো, তারপর `fetch()` দিয়ে ডাটা পাঠাতে হতো।

### 💡 React 19 কী করল?

এখন আপনি ফর্মের মধ্যেই সরাসরি সার্ভারের ফাংশন কল করতে পারবেন!

* **"use server":** সার্ভারের কোড (রান্নাঘর)।
* **"use client":** ইউজার ইন্টারফেস (ডাইনিং)।

**কোড উদাহরণ:**

```jsx
// action.js (সার্ভার সাইড)
"use server";

export async function submitData(formData) {
  const name = formData.get("username");
  await db.user.create({ name }); // সরাসরি ডাটাবেসে সেভ!
}

```

```jsx
// Form.jsx (ক্লায়েন্ট সাইড)
import { submitData } from "./action";

export default function MyForm() {
  return (
    // কোনো fetch বা axios লাগবে না, সরাসরি অ্যাকশন বসিয়ে দিন
    <form action={submitData}>
      <input name="username" type="text" />
      <button type="submit">Save</button>
    </form>
  );
}

```

---

## 4. Form Actions (অটোমেটিক ম্যানেজার)

### 🤔 সমস্যা কী ছিল?

বাটনে ক্লিক করার পর "Loading..." দেখানোর জন্য আমাদের আলাদা `useState` বানাতে হতো (`isLoading` true/false)।

### 💡 React 19 কী করল?

**`useFormStatus`** হুক ব্যবহার করলে React নিজেই বলে দেয় ফর্ম এখন লোড হচ্ছে কিনা।

**কোড উদাহরণ:**

```jsx
import { useFormStatus } from "react-dom";

function SubmitButton() {
  const { pending } = useFormStatus(); // অটোমেটিক লোডিং স্ট্যাটাস

  return (
    <button disabled={pending}>
      {pending ? "Saving..." : "Save Now"}
    </button>
  );
}

```

---

## 5. useOptimistic (আশাবাদী হওয়া)

### 🤔 সমস্যা কী ছিল?

ইন্টারনেট স্লো থাকলে লাইক বাটনে ক্লিক করার পর ১-২ সেকেন্ড অপেক্ষা করতে হতো আপডেট দেখার জন্য।

### 💡 React 19 কী করল?

**`useOptimistic`** হুক সার্ভারে ডাটা পৌঁছানোর আগেই UI আপডেট করে দেয়। ইউজার ভাববে অ্যাপ সুপার ফাস্ট!

**কোড উদাহরণ:**

```jsx
import { useOptimistic } from "react";

function Chat({ messages }) {
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (state, newMessage) => [...state, newMessage] // সাথে সাথে মেসেজ যোগ হবে
  );

  const sendMessage = async (formData) => {
    const text = formData.get("text");
    addOptimisticMessage(text); // ১. UI তে দেখাও
    await saveToServer(text);   // ২. সার্ভারে পাঠাও
  };

  return (
    <div>
      {optimisticMessages.map((msg, i) => <p key={i}>{msg}</p>)}
      <form action={sendMessage}>
        <input name="text" />
        <button>Send</button>
      </form>
    </div>
  );
}

```

---

## 6. No more forwardRef (মিডলম্যান বিদায়)

### 🤔 সমস্যা কী ছিল?

অন্য কম্পোনেন্টে `ref` পাঠাতে হলে `forwardRef` ব্যবহার করতে হতো, যা দেখতে বিদঘুটে ছিল।

### 💡 React 19 কী করল?

এখন `ref` কে সাধারণ `props` এর মতোই পাঠানো যায়।

**কোড তুলনা:**

❌ **আগে:**

```jsx
const MyInput = forwardRef((props, ref) => {
  return <input ref={ref} {...props} />;
});

```

✅ **এখন:**

```jsx
const MyInput = ({ ref, ...props }) => {
  return <input ref={ref} {...props} />;
};

```

---

## 7. Context API (সহজ উপহার)

### 🤔 সমস্যা কী ছিল?

আগে `<Context.Provider>` লিখতে হতো। বারবার `.Provider` লেখা বিরক্তিকর ছিল।

### 💡 React 19 কী করল?

এখন শুধু `<Context>` লিখলেই হবে।

**কোড তুলনা:**

❌ **আগে:**

```jsx
<ThemeContext.Provider value="dark">
  <App />
</ThemeContext.Provider>

```

✅ **এখন:**

```jsx
<ThemeContext value="dark">
  <App />
</ThemeContext>

```

---

### ❤️ শেষ কথা

React 19 ডেভেলপারদের জীবন সহজ করার জন্য এসেছে। কোড হবে কম, কাজ হবে বেশি! আশা করি এই গাইডটি আপনার উপকারে আসবে।

Happy Coding! 🚀
