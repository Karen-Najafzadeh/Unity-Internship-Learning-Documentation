You need an **SSH key** to securely connect your local machine to a **GitLab** repository. This allows you to **push, pull, and clone repositories** without entering your username and password every time.  

---

## **🔹 Steps to Set Up an SSH Key for GitLab**
### **1️⃣ Check for Existing SSH Keys**
Before creating a new SSH key, check if you already have one:  
🔹 Open a terminal and run:
```sh
ls -al ~/.ssh
```
If you see files like `id_rsa` and `id_rsa.pub`, you already have an SSH key.

---

### **2️⃣ Generate a New SSH Key (if needed)**
If you don’t have an SSH key, create one with:
```sh
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
- **`-t rsa`**: Uses the RSA algorithm (recommended).  
- **`-b 4096`**: Sets the key size to **4096 bits** (for security).  
- **`-C "your_email@example.com"`**: Adds an **email identifier**.  

Press **Enter** to save the key in the default location:  
```
~/.ssh/id_rsa
```
You can set a **passphrase** (optional, for extra security).

---

### **3️⃣ Add the SSH Key to the SSH Agent**
Start the SSH agent:
```sh
eval "$(ssh-agent -s)"
```
Then add your SSH key:
```sh
ssh-add ~/.ssh/id_rsa
```

---

### **4️⃣ Copy the SSH Public Key**
Run:
```sh
cat ~/.ssh/id_rsa.pub
```
or use:
```sh
clip < ~/.ssh/id_rsa.pub  # For Windows (Git Bash)
pbcopy < ~/.ssh/id_rsa.pub # For macOS
```
This copies your public key.

---

### **5️⃣ Add SSH Key to GitLab**
1. **Go to GitLab** and log in.  
2. Click on your **Profile Picture > Preferences**.  
3. Navigate to **SSH Keys** (or **Settings > SSH Keys**).  
4. **Paste your SSH public key** (`id_rsa.pub`) into the provided field.  
5. **Click "Add Key"**.  

---

### **6️⃣ Test SSH Connection**
Run:
```sh
ssh -T git@gitlab.com
```
If successful, you’ll see:
```
Welcome to GitLab, @your_username!
```

---

### **7️⃣ Clone a Repository via SSH**
Now you can clone repositories using SSH:
```sh
git clone git@gitlab.com:username/repository.git
```
Replace **username/repository.git** with your project’s **SSH URL**.

---

## **✨ Troubleshooting**
1. **Permission denied (publickey)**  
   - Ensure your SSH key is **added to GitLab**.  
   - Check if SSH is **running**: `eval "$(ssh-agent -s)"`  
   - Try **re-adding** your key: `ssh-add ~/.ssh/id_rsa`  

2. **SSH key not found**  
   - Run: `ls ~/.ssh/` → Ensure `id_rsa` and `id_rsa.pub` exist.  
   - If missing, **generate a new key** (Step 2).  

---

Now you’re all set! 🚀 You can push, pull, and manage repositories on **GitLab** securely using SSH.