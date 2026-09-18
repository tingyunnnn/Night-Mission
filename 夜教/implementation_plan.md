# Implementation Plan for ZPD Database Updates

## Goal Description
Update the [system.html](file:///c:/Users/User/Desktop/%E5%A4%9C%E6%95%99/system.html) file to reflect a "Zootopia Police Department (ZPD)" theme, add multiple case files with hidden suspect details, implement a multi-user login system with role-based permissions, and add UI functionalities like a "Back" button and a "Logout" button. The updates must maintain the existing dark mode, glassmorphism, and jumpscare mechanics.

## Proposed Changes

### [c:/Users/User/Desktop/夜教/system.html](file:///Users/User/Desktop/%E5%A4%9C%E6%95%99/system.html)
Modify the single HTML file to encapsulate all new logic, content, and UI changes.

#### 1. UI & Thematic Updates (HTML/CSS)
- **Header:** Update Logo text to "ZPD 國家機密調查系統". Change Officer text to display dynamically based on the logged-in user. Add a Logout (`登出`) button next to the user name.
- **Sidebar:** Update menu items to: "系統首頁", "案卷調閱系統", "法醫鑑定報告", "全國通緝名單" (UI only).
- **Bulletin Board (`內部機密通報`):** Replace existing entries with:
  - `[人事異動] 4/15 茱蒂警官因重大執法疏失，榮譽一等獎撤銷公告，即日生效。`
  - `[內部通報] 5/6 馬市長公開表揚重案組迅速偵破 5/5 高級公寓樹懶命案。`
  - `[警告] 近期發現黑市器官交易流竄，各單位請加強夜間巡邏。`
  - `[系統公告] 伺服器將於本週五凌晨進行安全升級。`
- **Right Widgets:** Update "Duty Officer" to "公牛局長 (Chief Bogo)".
- **Version History Button:** Change styling to be more visible (e.g., text-gray-500 hover:text-gray-300 instead of extreme opacity reduction).

#### 2. Multi-Account System & Permissions (JS)
- Remove hardcoded `loginAcc` and `loginPass`.
- Implement a user database array:
  - **Level 5 (Full Access):** `inspector.k@zpd.gov` / `A7b9X2qM` (特級調查員)
  - **Level 2 (Basic Access - 5 accounts):** 
    - `officer.judy@zpd.gov` / `Carrot01`
    - `officer.clawhauser@zpd.gov` / `Donut123`
    - `chief.bogo@zpd.gov` / `Buffalo9`
    - `detective.mchorn@zpd.gov` / `Rhino555`
    - `officer.delgato@zpd.gov` / `Lion007` (All "基層警員")
- Store active user data on successful login to dynamically update header (Name & Level).
- Apply permission checks during search:
  - Level 5 can see all cases (`CASE73926481` + the 5 new cases).
  - Level 2 can only see the 5 new cases. Any attempt to search `CASE73926481` returns permission denied. (Assuming the main hidden case remains exclusive to Level 5).

#### 3. New Case Files & Navigation (HTML/JS)
- **Data Structure:** Create a JS object/array holding the details for the 5 new cases (`CASE-58291047`, `CASE-63918274`, `CASE-27485910`, `CASE-81930472`, `CASE-91028346`).
- **Dynamic Render:** Modify the search submission logic to dynamically populate a generic `case-details-template` instead of hardcoding the HTML for a single case, or hide/show specific predefined divs. Dynamic rendering is cleaner.
- **Suspect Details Button:** Inside each new case profile, add a "View Suspect Details" button. Clicking this reveals a hidden section within the card displaying the suspect info.
- **Back Button:** Add a "回上頁 / 返回搜尋" button within the case details view that hides the case details and shows the search box again.
- **Logout Logic:** The logout button in the header resets the view to `login-view`, clears session variables, and resets search inputs.

## Verification Plan

### Manual Verification
Since this is a client-side HTML file, verification will be done manually in the browser.
1. **Login Tests:**
   - Test incorrect password (triggers Jumpscare 1).
   - Test 3 incorrect passwords (triggers 180s lockdown).
   - Login with Level 2 account (e.g., `officer.judy@zpd.gov`). Verify Header shows correct name/level.
   - Login with Level 5 account (`inspector.k@zpd.gov`). Verify Header shows "特級調查員 / Level 5".
2. **Search Tests:**
   - As Level 2: Search `CASE-58291047`. Verify case shows up. Search `CASE73926481` (Original secret case), expect "Permission Denied".
   - As Level 5: Search `CASE73926481`. Verify original hidden case shows up with Version History button. Search new cases, verify they show up.
   - Search invalid string. Expect "Not found" error.
3. **UI/Interaction Tests:**
   - In a new case file, click "View Suspect" to ensure it toggles the suspect profile correctly.
   - Click "Back" button to ensure case details hide and search form reappears.
   - Click "Logout" button to ensure return to login screen.
   - Check if the "Version History v1.0" text is visibly gray and legible.
   - Read bulletin board to verify text changes.
