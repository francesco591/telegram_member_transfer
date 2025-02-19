# Telegram Account Manager & Member Transfer

## 📌 Project Overview

This project is a script for managing Telegram accounts and transferring members from source groups to a destination group. It uses **Telethon** for interacting with Telegram and **Rich** for better console output.

## ⚙️ Features

- **Manage Telegram Accounts**: Add, remove, and check account status
- **Member Transfer Between Groups**: Move users from source groups to a target group
- **API Management**: Add and remove Telegram APIs
- **Anti-Spam Limitations Handling**: Avoid bans and manage FloodWait errors
- **Logging System**: Logs stored in `telegram_manager.log`
- **Beautiful CLI Interface**: Uses **Rich** for improved UI

## 🛠 Installation & Setup

### 1️⃣ Prerequisites

Before running this script, install the required dependencies:

```sh
pip install telethon rich
```

### 2️⃣ Initial Setup

#### 📌 **`accounts.json` File Structure**

To store the API accounts and connected Telegram accounts, create a `accounts.json` file with the following structure:

```json
{
    "api_accounts": [
        {
            "api_id": 12345678,
            "api_hash": "12g45f89asd01ff12asd4151bba71c1asda0",
            "accounts": [
                {
                    "phone": "+981234567811",
                    "session": "session_7710.session"
                }
            ]
        }
    ]
}
```

**Field Descriptions**:
- `api_accounts`: List of Telegram APIs used to manage accounts.
- `api_id` & `api_hash`: API credentials from [my.telegram.org](https://my.telegram.org).
- `accounts`: List of phone numbers linked to each API.
- `phone`: The phone number of the account.
- `session`: The session file of the Telegram account.

## 🎯 Running the Script

### Running the Main Menu:

```sh
python main.py
```

After running the script, a menu will appear, allowing you to choose an operation:

1. **Manage Accounts**: Add/remove APIs and accounts
2. **Transfer Members**: Transfer members from source groups to the destination group
3. **Exit**: Close the program

## 🔧 How It Works

### **1️⃣ Managing Telegram Accounts**
You can manage APIs and Telegram accounts through the menu:

✅ **Add API**: Register `api_id` and `api_hash`  
✅ **Add Account**: Login using a phone number and save the session  
✅ **Remove API**: Delete an API from the system  
✅ **Remove Account**: Remove a specific phone number from an API

### **2️⃣ Transferring Group Members**
This feature allows you to move members from one or multiple source groups to a destination group.

1. **Select Source Groups**
2. **Select the Destination Group**
3. **Choose the Number of Members to Transfer (Based on API & Telegram Limits)**
4. **Start the Transfer Process with Anti-Spam Measures**

🔴 **Limitations**:
- Each account can only invite **50 members per day**.
- If Telegram raises a **FloodWaitError**, the program will wait automatically.


## 🛠 Core Algorithms

### ✅ **Group Analysis Algorithm**
Before transferring, the script analyzes the number of members in the source group and suggests an optimal transfer limit:

```python
async def analyze_group(client, source_group):
    source_entity = await client.get_entity(source_group)
    members = await client.get_participants(source_entity)
    total_members = len(members)
    suggested_transfer = min(total_members, 50 * len(load_data()["api_accounts"]))
    return min(int(input(f"How many members do you want to transfer? (Recommended: {suggested_transfer}): ")), total_members)
```

### ✅ **Member Transfer Algorithm**
- Randomly selects members from source groups
- Uses multiple accounts to distribute invites
- Checks daily limits and manages **FloodWaitError**
- Introduces random delays between invites to avoid bans

```python
async def transfer_members():
    destination_entity = await clients[0].get_entity(destination_group)
    max_invites_per_account = 50

    for member in members:
        client = clients[transferred_count % len(clients)]
        try:
            await client(InviteToChannelRequest(destination_entity, [member]))
            transferred_count += 1
            time.sleep(random.uniform(10, 30))  # Avoid getting banned
        except FloodWaitError as e:
            time.sleep(e.seconds)
```

## 📝 Logging System

The program logs all key events in **`telegram_manager.log`**:

✅ **Error logging**  
✅ **Blocked numbers detection**  
✅ **Successful operations logging**  

Sample log entry:

```
2025-02-19 12:34:56 - INFO - Phone number +981234567811 added successfully.
2025-02-19 12:35:10 - WARNING - Phone number +981234567811 is banned.
2025-02-19 12:36:45 - INFO - Added user 123456789 to group @destination.
```

## ❗ Security Notes
- Never share your `api_id` and `api_hash` publicly.
- Do not use newly created accounts for member transfers as they get banned quickly.
- Introduce **random delays** between invitations to avoid being blocked.

## 📜 License

This project is released under the **MIT** license.
