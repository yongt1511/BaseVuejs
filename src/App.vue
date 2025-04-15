<script setup>
import { reactive } from 'vue';
import { message } from 'ant-design-vue';
import BaseFormComponent from './components/common/form/BaseFormComponent.vue'
import BaseInputComponent from './components/common/inputs/BaseInputComponent.vue'

// Initial form values
const initialValues = reactive({
 username: '',
 password: '',
 confirmPassword: '',
 email: '',
 age: null,
 bio: ''
});
// Form validation rules
const rules = {
 username: [
  { required: true, message: 'Please input your username!', trigger: ['change', 'blur'] },
  { min: 3, max: 20, message: 'Username must be between 3 and 20 characters', trigger: ['change', 'blur'] }
 ],
 password: [
  { required: true, message: 'Please input your password!', trigger: 'blur' },
  { min: 6, message: 'Password must be at least 6 characters', trigger: 'blur' }
 ],
 confirmPassword: [
  { required: true, message: 'Please confirm your password!', trigger: 'blur' },
  {
 validator(rule, value) {
  console.log(value)
  if (!value || getFieldValue('password') === value) {
   return Promise.resolve();
  }
  return Promise.reject('The two passwords do not match!');
 },
 trigger: 'blur'
  }
 ],
 email: [
  { required: true, message: 'Please input your email!', trigger: 'blur' },
  { type: 'email', message: 'Please enter a valid email address', trigger: 'blur' }
 ],
 age: [
  { required: true, message: 'Please input your age!', trigger: 'blur' },
  { type: 'number', min: 18, message: 'You must be at least 18 years old', trigger: 'blur' }
 ]
};

// Form submission handler
const onFinish = (values) => {
 console.log('Form submitted:', values);
 // Here you would typically send the data to your API
};

// Form submission failure handler
const onFinishFailed = ({ errorFields }) => {
 console.log('Form validation failed:', errorFields);
 // message.error('Please fix the errors in the form');
};
</script>
<template>
 <div class="form-example">
  <h1>User Registration</h1>
  <BaseFormComponent
      :initial-values="initialValues"
      :rules="rules"
      @finish="onFinish"
      @finishFailed="onFinishFailed"
      submit-text="Register"
      success-message="Registration successful!"
  >
   <!-- Text input for username -->
   <BaseInputComponent
       name="username"
       label="Username"
       v-model="initialValues.username"
       placeholder="Enter your username"
       allow-clear
   />

   <!-- Password input -->
   <BaseInputComponent
       name="password"
       label="Password"
       type="password"
       v-model="initialValues.password"
       placeholder="Enter your password"
   />

   <!-- Confirm password input -->
   <BaseInputComponent
       name="confirmPassword"
       label="Confirm Password"
       type="password"
       v-model="initialValues.confirmPassword"
       placeholder="Confirm your password"
   />

   <!-- Email input -->
   <BaseInputComponent
       name="email"
       label="Email"
       v-model="initialValues.email"
       placeholder="Enter your email"
       allow-clear
   />

   <!-- Number input for age -->
   <BaseInputComponent
       name="age"
       label="Age"
       type="number"
       v-model="initialValues.age"
       placeholder="Enter your age"
       :min="0"
       :max="120"
   />

   <!-- Textarea for bio -->
   <BaseInputComponent
       name="bio"
       label="Biography"
       type="textarea"
       v-model="initialValues.bio"
       placeholder="Tell us about yourself"
       :auto-size="{ minRows: 3, maxRows: 6 }"
       :show-count="true"
       :max-length="500"
   />

   <template #actions>
    <a-button>Cancel</a-button>
   </template>
  </BaseFormComponent>
 </div>
</template>
<style scoped>
.form-example {
 max-width: 800px;
 margin: 0 auto;
 padding: 20px;
}

h1 {
 margin-bottom: 24px;
 text-align: center;
}
</style>
