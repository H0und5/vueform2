<template>
  <form>

    <!-- Account Information -->

    <label>Email:</label>
    <input type="email" required v-model="email" placeholder="Enter your email here">

    <label>Password:</label>
    <input type="password" required v-model="password" placeholder="Enter your password here">

    <!-- Select Role -->
    
    <label>Role:</label>
    <select v-model="role" required>
      <option value="" disabled hidden selected>Choose Role</option>
      <option value="Developer">Web Developer</option>
      <option value="Designer">Web Designer</option>
    </select>

    <!-- Skills -->

    <label>Skills:</label>
    <input type="text" required v-model="tempSkill" @keyup="addSkill" placeholder="Add each skill with a comma at the end"/>
    <div v-for="skill in skills" :key="skill" class="pill" @click="deleteSkill">
      {{ skill }}
    </div>

    <!-- Accept Terms and Conditions -->

    <div class="terms">
      <input type="checkbox" v-model="terms" required>
      <label>Accept Terms and Conditions</label>
    </div>

    <!-- Form Submit Button -->

    <div class="submit">
      <button type="submit" @click.prevent="submitFormHandler">Submit Form</button>
    </div>
  </form>

  <p>Email: {{ email }}</p>
  <p>Password: {{ password }}</p>
  <p>Role: {{ role }}</p>
  <p>Terms accepted: {{ terms }}</p>
  <div v-for="skill in skills" class="skills">
    <p>{{ skill }}</p>
  </div>
</template>

<script>
import router from '@/router';

  export default {
    data() {
      return {
        email: '',
        password: '',
        role: '',
        terms: false,
        tempSkill: '',
        skills: [],

        }
      },

    methods: {
      addSkill(e) {
        if (e.key === ',' && this.tempSkill !== '') {
          if(!this.skills.includes(this.tempSkill.substring(0, this.tempSkill.length - 1))) {
            this.skills.push(this.tempSkill.substring(0, this.tempSkill.length - 1));
          } 
          this.tempSkill = '';
        }
      },

      deleteSkill(e) {

        let skillToDelete = e.target.textContent;

        console.log(skillToDelete);

        this.skills = this.skills.filter(item => item != skillToDelete);

        console.log(this.skills)
        
      },

      async submitFormHandler() {
        if (this.terms === true) {

          let userData = {
            email: this.email, 
            password: this.password,
            role: this.role,
            skills: this.skills,
          }

          try {
            const response = await fetch('https://vueform-5b842-default-rtdb.firebaseio.com/users.json', {
              method: 'POST',
              body: JSON.stringify(userData)
            });

            console.log('Response Status: ', response.status);
            console.log(response);

            if (response.status === 200) {
              router.push('/dashboard')
            }

          } catch (err) {
            console.log(err);
          }
        

          this.email = '';
          this.password = '';
          this.role = '';
          this.skills = [];

        } else {
            alert('Please complete the form :D!');
            console.log('Accept Terms and Conditions')
        }
      },
    }
    }
</script>

<style>
  form {
    max-width: 420px;
    margin: 30px auto;
    background: white;  
    text-align: left;
    padding: 40px;
    border-radius: 10px;
  }
  label {
    color: #aaa;
    display: inline-block;
    margin: 25px 0 15px;
    font-size: 0.6em;
    text-transform: uppercase;
    letter-spacing: 1px;
    font-weight: bold;
  }
  input, select {
    display: block;
    padding: 10px 6px;
    width: 100%;
    box-sizing: border-box;
    border: none;
    border-bottom: 1px solid #ddd;
    color: #555;
  }
  input[type="checkbox"] {
    display: inline-block;
    width: 16px;
    margin: 0 10px 0 0;
    position: relative;
    top: 2px;
  }
  .pill {
    display: inline-block;
    margin: 20px 10px 0 0; 
    padding: 6px 12px; 
    background: #eee;
    border-radius: 20px;
    font-size: 0.8rem;
    letter-spacing: 1px;
    font-weight: bold;
    color: #777;
    cursor: pointer;
  }
  .submit {
    display: flex;
    justify-content: center;
    margin: 10px;
  }
  .submit button {
    background: rgb(34, 128, 244);
    padding: 6px 12px;
    border-radius: 20px;
    color: #fff;
    box-sizing: border-box;
    border: 2px solid rgb(34, 128, 244);
    cursor: pointer;
  }
  .skills {
    display: inline-block;
  }
</style>