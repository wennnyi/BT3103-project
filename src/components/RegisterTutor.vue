<template>
  <div>
    <img id="logo" :src="logo" />
    <nav>
      <ul class="navbar" style="list-style-type: none;">
        <li><router-link to="/">Home</router-link></li>
        <li><router-link to="/login">Login</router-link></li>
      </ul>
    </nav>
    <div>
      <h1>Register Your Account</h1>
    </div>
    <div style="text-align:center" class="formdiv">
      <form @submit.prevent="register">
        <label for="first" >First Name</label><br />
        <input
          type="text"
          placeholder="First Name"
          v-model="first"
          required
        /><br /><br />
        <label for="last" >Last Name</label><br />
        <input
          type="text"
          placeholder="Last Name"
          v-model="last"
          required
        /><br /><br />
        <label for="phone" >Phone Number</label><br />
        <input
          v-model.number="phone"
          type="number"
          placeholder="Phone Number"
          required
        /><br /><br />
        <label for="qualifications" 
          >Your Qualifications</label
        ><br />
        <textarea
          v-model="qualifications"
          placeholder="Your qualifications"
          required
        ></textarea
        ><br />
        <p >Teaching Level</p>
        <input
          type="checkbox"
          id="primary"
          value="Primary"
          v-model="level"
          class="check"
        />
        <label for="primary">Primary</label>
        <input
          type="checkbox"
          id="secondary"
          value="Secondary"
          v-model="level"
          class="check"
        />
        <label for="secondary">Secondary</label>
        <input
          type="checkbox"
          id="jc"
          value="JC"
          v-model="level"
          class="check"
        />
        <label for="jc">JC</label>
        <input
          type="checkbox"
          id="university"
          value="University"
          v-model="level"
          class="check"
        />
        <label for="university">University</label>
        <br /><br /><br />
        <label for="experience" 
          >Years of Experience</label
        ><br />
        <input
          v-model.number="experience"
          type="number"
          min="0"
          max="60"
          oninput="validity.valid||(value='');"
          required
        /><br /><br />
        <p >Teaching Subjects</p>
        <input
          type="checkbox"
          id="mathematics"
          value="Mathematics"
          v-model="subject"
          class="check"
        />
        <label for="mathematics">Mathematics</label>
        <input
          type="checkbox"
          id="english"
          value="English"
          v-model="subject"
          class="check"
        />
        <label for="english">English</label>
        <input
          type="checkbox"
          id="physics"
          value="Physics"
          v-model="subject"
          class="check"
        />
        <label for="physics">Physics</label>
        <input
          type="checkbox"
          id="chemistry"
          value="Chemistry"
          v-model="subject"
          class="check"
        />
        <label for="chemistry">Chemistry</label>
        <input
          type="checkbox"
          id="biology"
          value="Biology"
          v-model="subject"
          class="check"
        />
        <label for="biology">Biology</label>
        <br /><br /><br />
        <label for="rates" >Rates</label><br />
        <input
          v-model.number="rates"
          type="number"
          placeholder="Hourly Rate"
          min="0"
          oninput="validity.valid||(value='');"
          required
        /><br /><br />
        <label for="qualifications" 
          >Your Available Days</label
        ><br />
        <textarea
          v-model="availability"
          placeholder="Your available days"
          required
        ></textarea
        ><br /><br />
        <label for="email" >Email Address</label><br />
        <input
          type="email"
          placeholder="Email address"
          v-model="email"
          required
        /><br /><br />
        <label for="password" >Password</label><br />
        <input
          type="password"
          placeholder="Password"
          v-model="password"
          required
        /><br /><br />
        <button type="submit" value="register">Register</button>
      </form>
    </div>
  </div>
</template>

<script>

import logo from "../assets/logo2.png"
import firebase from "../firebase";
var db = firebase.firestore();

var pastThirtyDays = [];
for (let i = 0; i < 30; i++) {
  var date = new Date(new Date().setDate(new Date().getDate() - i))
    .toDateString()
    .slice(4);
  pastThirtyDays.push(date);
}
pastThirtyDays = pastThirtyDays.sort((a, b) => new Date(a) - new Date(b));

var clickHistory = {};
var viewHistory = {};

for (const date of pastThirtyDays) {
  clickHistory[date] = 0;
  viewHistory[date] = 0;
}

export default {
  name: "RegisterTutor",
  data() {
    return {
      first: "",
      last: "",
      email: "",
      password: "",
      qualifications: "",
      subject: [],
      rates: "",
      phone: "",
      availability: "",
      experience: "",
      image: "",
      level: [],
      engaging: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
      communication: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
      listening: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
      patience: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
      overall: [0, 0, 0, 0, 0],
      logo: logo
    };
  },
  methods: {
    register() {
      firebase
        .auth()
        .createUserWithEmailAndPassword(this.email, this.password)
        .then((user) => {
          var tutorid = user.user.uid;
          console.log(tutorid);
          db.collection("profiles")
            .doc(user.user.uid)
            .set({
              first_name: this.first,
              last_name: this.last,
              email: this.email,
              qualifications: this.qualifications,
              subject: this.subject,
              rates: this.rates,
              phone: this.phone,
              rate: 0,
              tutid: tutorid,
              availability: this.availability,
              experience: this.experience,
              image: this.image,
              level: this.level,
              engaging: this.engaging,
              communication: this.communication,
              listening: this.listening,
              patience: this.patience,
              overall: this.overall,
              profileViews: 0,
              contactClicks: 0,
              createDate: new Date(),
              clickHistory: clickHistory,
              viewHistory: viewHistory,
              mystudents: [],
            })
            .then(function() {
              console.log("Document successfully written!");
            })
            .catch(function(error) {
              console.error("Error writing document: ", error);
            });
          alert("Successfully registered!");

          this.$router.push("/homeTutor");
        })
        .catch((error) => {
          alert(error.message);
        });
    },
  },
};
</script>

<style scoped>

h1 {
    text-align: center;
    font-size: 64px;
    padding-top: 100px;
    margin: 10px auto;
}

#logo {
  float: left;
  padding-left:20px;
  padding-top: 15px;
  height: 100px;
  width: 95px;
  top:50px;
}

.formdiv {
  border-radius: 25px;
  border: 4px solid black;
  padding: 20px;
  background-image: linear-gradient(#55c9c2, white);
  width: 700px;
  margin: 70px auto;
  border-style: outset;
  font-weight: bold;
}

label {
    margin: 10px auto;
    font-size: 16pt;
}

p {
    margin: 15px auto;
    font-size: 16pt;
}

input[type="checkbox"] {
  /* Double-sized Checkboxes */
  -ms-transform: scale(2); /* IE */
  -moz-transform: scale(2); /* FF */
  -webkit-transform: scale(2); /* Safari and Chrome */
  -o-transform: scale(2); /* Opera */
  transform: scale(2);
  padding: 10px;
  margin-right: 10px;
  margin-left: 20px;
}

/* Might want to wrap a span around your checkbox text */
.checkboxtext {
  /* Checkbox text */
  font-size: 18pt;
  display: inline;
}

input:not(.check),
select {
  height: 40px;
  width: 200 px;
  font-size: 14pt;
  margin: 5px 0;
  padding: 8px;
  border-radius: 10px;
  box-shadow: 2px 1px;
}

textarea {
  height: 60px;
  width: 240px;
  font-size: 14pt;
  margin: 5px 0;
  padding: 8px;
  border-radius: 10px;
  box-shadow: 2px 1px;
}

button {
    font-family: Montserrat;
    font-weight: bold;
    font-size: 24px;
    line-height: normal;
    border-radius: 20px;
    padding: 7px 35px;
    background: #3a938d;
    cursor: pointer;
    box-shadow: 0 4px 4px rgba(0, 0, 0, 0.25);
}

button:hover {
    transform: scale(1.01,1.01);
    box-shadow: 0 8px 8px rgba(0, 0, 0, 0.472);
}

input,
textarea:focus {
  outline: none;
}

nav {
  list-style-type: none;
  margin: 10px;
  padding: 0;
  overflow: hidden;
  color: black;
  float: right;
  display: block;
  text-align: center;
  padding: 14px 16px;
  text-decoration: none;
  font-weight: bold;
}

nav li {
  float: left;
}

nav a {
  display: block;
  text-align: center;
  padding: 14px 16px;
  text-decoration: none;
  font-weight: bold;
}

</style>
