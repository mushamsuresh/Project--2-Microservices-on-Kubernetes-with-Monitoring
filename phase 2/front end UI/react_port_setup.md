# React Port Setup Guide

This guide explains how to check ports and configure React to run on a different port if needed.

---

## 🔹 Method 1: Check with netstat

1. Open **Command Prompt** as Administrator.
2. Run:
   ```bash
   netstat -ano | findstr :3000
   ```
   - This checks if port 3000 is in use.
   - Output looks like:
     ```
     TCP    0.0.0.0:3000   0.0.0.0:0   LISTENING   1234
     ```
     - `1234` is the PID (process ID).
3. To find which app is using it:
   ```bash
   tasklist /FI "PID eq 1234"
   ```

---

## 🔹 Method 2: Free the Port (if React is stuck)

Sometimes the React app doesn’t close properly.

To kill a process:
```bash
taskkill /PID 1234 /F
```

---

## 🔹 Method 3: Configure React to run on a different port

### Method 3.1: Temporary (Command Line)

Run React on a different port just for this session:
```bash
set PORT=3001 && npm start
```
- Windows (Command Prompt / PowerShell)
- Your app will now open at: `http://localhost:3001`

### Method 3.2: Automatic Port Selection by React

- When you run:
```bash
npm start
```
React tries **port 3000**.

- If 3000 is already in use, you’ll see something like:
```
Something is already running on port 3000.
Would you like to run the app on another port instead? » (Y/n)
```
- Press **Y** → React picks the next free port, e.g., 3001, 3002, etc.
- Opens your app at the chosen port, e.g., `http://localhost:3001`

### Method 3.3: Detect Free Ports Before Running

Check which ports are free using Command Prompt:
```bash
netstat -ano | findstr :3000
netstat -ano | findstr :3001
netstat -ano | findstr :3002
```
- Check sequentially until you find a port that’s **not in use**.

Then run React on that port temporarily:
```bash
set PORT=3001 && npm start
```

### Method 3.4: Persistent Port Using `.env`

1. In your React project root (`front_end/`), create a file called `.env`
2. Add this line:
```
PORT=3001
```
3. Save and run:
```bash
npm start
```
- React will always use **port 3001** for this project.

---

✅ Tip: React will always suggest the next available port if the default is busy, but using `.env` is cleaner for multiple projects.

