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
                <td>{{ user.username }}</td>
                <td>{{ user.email }}</td>
                <td>{{ user.phone }}</td>
                <td>{{ user.id }}</td>
                <td>{{ user.creationDate }}</td>
                <td>
                  <!-- Inside your template -->
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
          <form @submit.prevent="handleSubmit">
            <div class="form-group">
              <label for="username">Username:</label>
              <input type="text" id="username" v-model="username" required />
            </div>
            <div class="form-group">
              <label for="password">Password:</label>
              <input
                type="password"
                id="password"
                v-model="password"
                required
              />
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
    handleSubmit() {
      if (this.activeTab === "accountCreation") {
        const newUser = {
          id: this.users.length + 1,
          username: this.username,
          email: `${this.username.toLowerCase()}@example.com`,
          phone: "N/A",
          creationDate: new Date().toISOString().split("T")[0],
        };

        this.users.push(newUser);
        console.log(`Created account for user: ${this.username}`);
      } else {
        console.log("Handling other logic for user details");
      }

      this.username = "";
      this.password = "";
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
  font-family: "Roboto", sans-serif;
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
  background-color: rgba(255, 255, 255, 0.9);
  border-radius: 15px;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  color: #333;
  padding: 40px;
  width: 80%;
  margin: 0 auto;
}

.tabs {
  list-style-type: none;
  margin: 0;
  padding: 0;
  color: #ffffff;
  background-color: rgba(0, 123, 255, 0.8);
  overflow: auto;
  white-space: nowrap;
  border-radius: 8px;
}

.tabs li {
  display: inline-block;
  padding: 15px 20px;
  cursor: pointer;
  transition: background-color 0.3s ease-in-out;
}

.tabs li:hover {
  background-color: rgba(0, 86, 179, 0.8);
}

.tabs li.active {
  background-color: rgba(0, 86, 179, 0.8);
  border-bottom: 2px solid #ffffff;
}

.tab-content {
  padding: 40px;
  border-radius: 15px;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  position: relative;
  z-index: 0;
  align-items: center;
  display: contents;
}

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
  background-color: rgba(0, 123, 255, 0.8);
  color: #fff;
}

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
  background-color: rgba(248, 249, 250, 0.9);
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 30%;
  box-sizing: border-box;
}

button {
  padding: 15px 30px;
  background-color: rgba(0, 123, 255, 0.8);
  color: #fff;
  border: none;
  cursor: pointer;
  border-radius: 8px;
  transition: background-color 0.3s ease-in-out;
}

button:hover {
  background-color: rgba(0, 86, 179, 0.8);
}

.navbar {
  position: sticky;
  top: 0;
  z-index: 1000;
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: rgba(0, 123, 255, 0.8);
  padding: 15px;
  border-radius: 8px;
  margin-bottom: 10px;
}

.brand {
  color: #fff;
  font-size: 1.5em;
  
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
}

.nav-links router-link.active {
  background-color: rgba(0, 86, 179, 0.8);
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
  width: 400px; /* Adjust the width as needed */
}

.popup-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #ccc;
  padding-bottom: 10px;
  margin-bottom: 10px;
}

.popup-header h2 {
  margin: 0;
}

.close-btn {
  cursor: pointer;
  font-size: 20px;
  color: #aaa;
}

.popup-body {
  text-align: left;
}

.popup-body p {
  margin: 10px 0;
}

.popup-body button {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease-in-out;
}

.popup-body button:hover {
  background-color: #0056b3;
}

/* Inside your existing style block */
.generate-report-btn {
  position: relative;
  padding: 10px 20px;
  border-radius: 7px;
  border: 1px solid rgb(61, 106, 255);
  font-size: 14px;
  text-transform: uppercase;
  font-weight: 600;
  letter-spacing: 2px;
  /* background: transparent; */
  color: #fff;
  overflow: hidden;
  box-shadow: 0 0 0 0 transparent;
  transition: all 0.2s ease-in;
}

.generate-report-btn:hover {
  background: rgb(61, 106, 255);
  box-shadow: 0 0 30px 5px rgba(0, 142, 236, 0.815);
  transition: all 0.2s ease-out;
}

.generate-report-btn:hover::before {
  animation: sh02 0.5s 0s linear;
}

.generate-report-btn::before {
  content: "";
  display: block;
  width: 0px;
  height: 86%;
  position: absolute;
  top: 7%;
  left: 0%;
  opacity: 0;
  background: rgb(255, 255, 255) 646;
  box-shadow: 0 0 50px 30px #fff;
  transform: skewX(-20deg);
  color: #333;
}

@keyframes sh02 {
  from {
    opacity: 0;
    left: 0%;
  }

  50% {
    opacity: 1;
  }

  to {
    opacity: 0;
    left: 100%;
  }
}

.generate-report-btn:active {
  box-shadow: 0 0 0 0 transparent;
  transition: box-shadow 0.2s ease-in;
}

.btn {
  display: inline-block;
  padding: 0.9rem 1.8rem;
  font-size: 16px;
  font-weight: 700;
  color: white;
  /* border: 5px solid rgb(88, 70, 252); */
  cursor: pointer;
  position: relative;
  background-color: transparent;
  text-decoration: none;
  overflow: hidden;
  z-index: 1;
  font-family: inherit;
  transition: all 0.3s;
}

.btn::before {
  content: "";
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: rgb(0 114 255);
  transform: translateX(-100%);
  transition: all 0.3s;
  z-index: -1;
}

.btn:hover::before {
  transform: translateX(0);
}

.btn.active {
  color: white;
}

.btn.active::before {
  transform: translateX(0);
}
</style>
