# React Component Import & Dynamic Swap Conversation

This document contains the full conversation explaining how React imports and renders components, including dynamic component swapping, kept in full detail for future reference.

---

## 1️⃣ React Doesn’t Automatically Run All Files

React only runs the code that is imported and rendered. By default:

**index.js** is the entry point of your app:

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

Here, `<App />` comes from `App.js`.

---

## 2️⃣ How Your addressform.js Works

If your `addressform.js` is in the `src` folder but not imported anywhere, React will ignore it.  

But if you see it working, it’s because `App.js` is importing it:

```javascript
import AddressForm from './addressform';

function App() {
  return (
    <div>
      <h1>My App</h1>
      <AddressForm />
    </div>
  );
}

export default App;
```

React includes and renders it in the app.

---

## 3️⃣ Key Points

- React does **not automatically run every file** in `src/`.
- Only files that are **imported and used** get bundled.
- File names don’t matter as long as you import them correctly.
- You can create many components in `src/`, but they must be **imported to render**.

✅ In this case, React takes `addressform.js` because `App.js` imports and uses it in JSX.

---

## 4️⃣ React Runs App.js and Imports

Yes, React basically runs `App.js` only and takes the imports from other files:

- **index.js** → Entry point, renders `<App />`
- **App.js** → Main container, imports other components
- **Other files** → Executed only if imported and used

---

## 5️⃣ Manually Changing App.js

To render a different component, you need to import it into `App.js`:

**Profile.js**
```javascript
function Profile() {
  return <h1>My Profile</h1>;
}
export default Profile;
```

**App.js**
```javascript
import Profile from './Profile';

function App() {
  return <Profile />;
}

export default App;
```

- Now React renders `Profile` instead of `AddressForm`.

- You can also render multiple components together:

```javascript
function App() {
  return (
    <div>
      <AddressForm />
      <Profile />
    </div>
  );
}
```

---

## 6️⃣ Dynamically Swap Components Without Editing App.js

### **Step 1: Component Files**

**AddressForm.js**
```javascript
function AddressForm() {
  return <h1>Address Form</h1>;
}
export default AddressForm;
```

**Profile.js**
```javascript
function Profile() {
  return <h1>Profile Page</h1>;
}
export default Profile;
```

**Dashboard.js**
```javascript
function Dashboard() {
  return <h1>Dashboard</h1>;
}
export default Dashboard;
```

---

### **Step 2: App.js with Component Map**

```javascript
import React, { useState } from 'react';
import AddressForm from './AddressForm';
import Profile from './Profile';
import Dashboard from './Dashboard';

const components = {
  AddressForm: <AddressForm />,
  Profile: <Profile />,
  Dashboard: <Dashboard />
};

function App() {
  const [current, setCurrent] = useState('AddressForm'); // default

  return (
    <div>
      <h1>React Dynamic Component Demo</h1>

      {/* Buttons to switch */}
      <button onClick={() => setCurrent('AddressForm')}>AddressForm</button>
      <button onClick={() => setCurrent('Profile')}>Profile</button>
      <button onClick={() => setCurrent('Dashboard')}>Dashboard</button>

      {/* Render selected component */}
      <div style={{ marginTop: '20px' }}>
        {components[current]}
      </div>
    </div>
  );
}

export default App;
```

---

### **Step 3: How It Works**

- `components` object maps names to components.
- `useState` keeps track of which component is selected.
- Clicking a button updates the state, and React renders the selected component automatically.

---

### ✅ Benefits

- No need to manually change imports/JSX every time.
- Useful for testing multiple components quickly.
- Clean and scalable for many components.  

---

This document preserves the full content of the conversation for future reference, including code examples and detailed explanations.  

