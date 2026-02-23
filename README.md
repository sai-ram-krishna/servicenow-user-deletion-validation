# servicenow-user-deletion-validation
# ServiceNow Project: Prevent User Deletion if Assigned to Incident

## 📌 Project Overview

In IT Service Management environments, users assigned to active incidents were being deleted.  
This caused:

- Broken data references
- Loss of accountability
- Workflow disruption
- Audit trail inconsistency

This project implements a Business Rule to prevent deletion of users assigned to incidents.

---

## 🛠 Platform Used

- ServiceNow
- Business Rules
- GlideRecord API

---

## 🎯 Problem Statement

The system lacked a validation mechanism to prevent deletion of users actively assigned to incidents.

---

## ✅ Solution Implemented

A **Before Delete Business Rule** was created on the `sys_user` table.

### Configuration:

- Table: sys_user
- When: Before
- Delete: Checked
- Advanced: Enabled

---

## 💻 Script Used

```javascript
(function executeRule(current, previous) {

    var incGr = new GlideRecord('incident');
    incGr.addQuery('assigned_to', current.sys_id);
    incGr.setLimit(1);
    incGr.query();

    if (incGr.next()) {
        gs.addErrorMessage('This user cannot be deleted because they are assigned to one or more incidents.');
        current.setAbortAction(true);
    }

})(current, previous);
