<template>
  <div>
    <div class="navbar">
      <div class="brand">
        <b><i>User Management</i></b>
      </div>
      <div class="nav-links">
        <router-link
          to="/user-details"
          class="btn"
          :class="{ active: activeTab === 'userDetails' }"
          @click="setActiveTab('userDetails')"
        >
          User Details
        </router-link>

        <router-link
          to="/account-creation"
          class="btn"
          :class="{ active: activeTab === 'accountCreation' }"
          @click="setActiveTab('accountCreation')"
        >
          Account Creation
        </router-link>
      </div>
    </div>

    <div class="container">
      <div class="user-management-dashboard">
        <div class="tab-content" v-if="activeTab === 'userDetails'">
          <input
            type="text"
            v-model="searchTerm"
            placeholder="Search by username"
          />
          <table>
            <thead>
              <tr>
                <th>Username</th>
                <th>Email</th>
                <th>Phone</th>
                <th>ID</th>
                <th>Creation Date</th>
                <th>Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="user in filteredUsers" :key="user.id">
                <td>
                  <div class="user-info">
                    <div class="avatar">
                      <img :src="user.avatar" alt="User Avatar" />
                    </div>
                    <div class="username">
                      {{ user.username }}
                    </div>
                  </div>
                </td>
                <td>{{ user.email }}</td>
                <td>{{ user.phone }}</td>
                <td>{{ user.id }}</td>
                <td>{{ user.creationDate }}</td>
                <td>
                  <button
                    @click="openUserReportModal(user)"
                    class="generate-report-btn"
                  >
                    Generate Report
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <div class="tab-content" v-else>
    <form @submit.prevent="handleSubmit" class="account-creation-form">
      <div class="form-group">
        <div class="form-row">
          <div class="form-field">
            <label for="username">Username:</label>
            <span class="required-symbol">*</span>
            <input type="text" id="username" v-model="username" required />
          </div>
          <div class="form-field">
            <label for="email">Email:</label>
            <input type="email" id="email" v-model="email" />
          </div>
        </div>
      </div>
      <div class="form-group">
        <div class="form-row">
          <div class="form-field">
            <label for="phone">Phone:</label>
            <input type="text" id="phone" v-model="phone" />
          </div>
          <div class="form-field">
            <label for="password">Password:</label>
            <span class="required-symbol">*</span>
            <input type="password" id="password" v-model="password" required />
          </div>
        </div>
      </div>
      <div class="form-group">
        <button type="submit">Create Account</button>
      </div>
    </form>
  </div>
      </div>
    </div>

    <!-- User Report Popup -->
    <div v-if="isUserReportPopupVisible" class="popup-overlay">
      <div class="popup-content">
        <h2>User Report</h2>
        <div class="avatar">
          <img
            :src="
              selectedUser ? selectedUser.avatar : 'https://i.pravatar.cc/300'
            "
            alt="User Avatar"
          />
        </div>
        <p>
          <strong>User:</strong> {{ selectedUser ? selectedUser.username : "" }}
        </p>
        <p>
          <strong>Email:</strong> {{ selectedUser ? selectedUser.email : "" }}
        </p>
        <p>
          <strong>Phone:</strong>
          {{ selectedUser ? selectedUser.phone : "N/A" }}
        </p>
        <p><strong>ID:</strong> {{ selectedUser ? selectedUser.id : "" }}</p>
        <p>
          <strong>Creation Date:</strong>
          {{ selectedUser ? selectedUser.creationDate : "" }}
        </p>

        <!-- Add other report details here -->

        <button @click="closeUserReportPopup">Close</button>
      </div>
    </div>

    <!-- User Creation Success Popup -->
    <div v-if="isAccountCreationSuccess" class="popup-overlay">
      <div class="popup-content success-popup">
        <h2>Account Created Successfully!</h2>
        <p>Your account has been created successfully.</p>
        <button @click="closeSuccessPopup">Close</button>
      </div>
    </div>
  </div>
</template>

<script>
import { mockUsers } from "./mockData";

export default {
  data() {
    return {
      activeTab: "userDetails",
      searchTerm: "",
      users: [],
      username: "",
      password: "",
      selectedUser: null,
      isUserReportPopupVisible: false,
      isAccountCreationSuccess: false,
      email: "",
      phone: "",
    };
  },
  computed: {
    filteredUsers() {
      if (this.searchTerm === "") {
        return this.users;
      }
      return this.users.filter((user) => {
        return (
          user.username.toLowerCase().includes(this.searchTerm.toLowerCase()) ||
          user.email.toLowerCase().includes(this.searchTerm.toLowerCase())
        );
      });
    },
  },
  methods: {
    fetchUserData() {
      this.users = [...mockUsers];
    },
    setActiveTab(tab) {
      this.activeTab = tab;
      if (tab === "userDetails" && this.users.length === 0) {
        this.fetchUserData();
      }
    },
    openUserReportModal(user) {
      console.log(`Generating report for user: ${user.username}`);
      this.selectedUser = user;
      this.isUserReportPopupVisible = true;
    },
    closeUserReportPopup() {
      this.selectedUser = null;
      this.isUserReportPopupVisible = false;
    },
    closeSuccessPopup() {
      this.isAccountCreationSuccess = false;
    },
    handleSubmit() {
      if (this.activeTab === "accountCreation") {
        const newUser = {
          id: this.users.length + 1,
          username: this.username,
          email: this.email || "N/A", // Use provided email or generate a default one
          phone: this.phone || "XXX-XXX-XXX", // Use provided phone or set to "N/A"
          creationDate: new Date().toISOString().split("T")[0],
          avatar: `https://i.pravatar.cc/300?u=${this.users.length + 1}`, // Unique avatar URL
        };

        this.users.push(newUser);
        console.log(`Created account for user: ${this.username}`);
        // Show the account creation success popup
        this.isAccountCreationSuccess = true;

        // After a delay of 1 second, scroll to the new user
        this.$nextTick(() => {
          const newUserRow = this.$refs[`user-${newUser.id}`];
          if (newUserRow && newUserRow.length > 0) {
            newUserRow[0].scrollIntoView({
              behavior: "smooth",
              block: "start",
            });
          }
        });
      } else {
        console.log("Handling other logic for user details");
      }

      this.username = "";
      this.password = "";
      this.email = ""; // Clear the email field after submission
      this.phone = ""; // Clear the phone field after submission
    },
  },
  created() {
    this.users = [...mockUsers];
    console.log("Fetched user data:", this.users);
  },
};
</script>

<style scoped>
body {
  margin: 0;
  padding: 0;
  font-family: "Lato", sans-serif;
  background-size: cover;
  background-position: center;
}

.container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 150px;
}

.user-management-dashboard {
  margin-top: 50px;
  background-color: #f4f4f4;
  border-radius: 15px;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  color: #333;
  padding: 40px;
  width: 80%;
  margin: 0 auto;
}

/* Styles for tabs and navbar */
.navbar {
  position: sticky;
  top: 0;
  z-index: 1000;
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #3498db;
  padding: 15px;
  border-radius: 8px;
  margin-bottom: 10px;
}

.brand {
  color: #fff;
  font-size: 1.5em;
  font-family: "Lato", sans-serif; /* Add Lato font to the brand */
}

.nav-links {
  display: flex;
}

.nav-links router-link {
  color: #fff;
  text-decoration: none;
  padding: 10px;
  border-radius: 4px;
  margin-right: 10px;
  transition: background-color 0.3s ease-in-out;
  font-family: "Lato", sans-serif; /* Add Lato font to the navbar links */
}

.nav-links router-link.active {
  background-color: #60e5ef;
}

/* Styles for the table */
table {
  width: 100%;
  border-collapse: collapse;
  border: 1px solid #ddd;
  margin-top: 30px;
}

th,
td {
  padding: 20px;
  border: 1px solid #ddd;
  text-align: left;
}

thead {
  background-color: #007bff;
  color: #fff;
}

/* Styles for form and buttons */
.form-group {
  margin-bottom: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

form label {
  display: block;
  margin-bottom: 8px;
}

input[type="text"],
input[type="password"] {
  padding: 15px;
  border: 1px solid #ddd;
  background-color: #f8f9fa;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 30%;
  box-sizing: border-box;
}

button {
  padding: 15px 30px;
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
  border-radius: 8px;
  transition: background-color 0.3s ease-in-out;
}

button:hover {
  background-color: #0056b3;
}

/* Popup Styles */
.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.popup-content {
  background: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  width: 400px;
}

/* Add styles for avatars */
.user-info {
  display: flex;
  align-items: center;
  justify-content: left;
  font-size: 18px;
  font-weight: bold;
  color: #333;
}

.avatar {
  margin-right: 10px;
}

.avatar img {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  object-fit: cover;
  margin-right: 20px;
}
/* Add a style for success popups */
.success-popup {
  background: #4caf50; /* Green background color */
  color: white;
}
.account-creation-form {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.account-creation-form .form-group {
  margin-bottom: 20px;
  width: 80%;
}

.account-creation-form .form-row {
  display: flex;
  justify-content: space-between;
}

.account-creation-form .form-field {
  width: 48%; /* Adjust the width as needed */
  position: relative;
}

.account-creation-form label {
  display: block;
  margin-bottom: 8px;
  font-size: 18px;
}

.account-creation-form .required-symbol {
  color: #ff0000; /* Adjust the color as needed */
  position: absolute;
  top: 0;
  right: 0;
  font-size: 18px;
  margin-left: 4px;
}

.account-creation-form input {
  padding: 17px;
    border: 1px solid #ddd;
    background-color: #f8f9fa;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    width: 100%;
    margin-right: 200px;
    box-sizing: border-box;
}

.account-creation-form button {
  padding: 15px 30px;
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
  border-radius: 8px;
  transition: background-color 0.3s ease-in-out;
}

.account-creation-form button:hover {
  background-color: #0056b3;
}
</style>
