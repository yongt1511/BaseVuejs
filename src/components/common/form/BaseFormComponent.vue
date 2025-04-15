<script setup>
import { ref, reactive, watch, computed, onMounted, nextTick, provide } from 'vue';
import {  message } from 'ant-design-vue';
import { cloneDeep } from 'lodash-es';

const props = defineProps({
 // Form configuration
 initialValues: {
  type: Object,
  default: () => ({})
 },
 rules: {
  type: Object,
  default: () => ({})
 },
 labelCol: {
  type: Object,
  default: () => ({ span: 6 })
 },
 wrapperCol: {
  type: Object,
  default: () => ({ span: 18 })
 },
 validateTrigger: {
  type: [String, Array],
  default: ['change', 'blur']
 },
 scrollToFirstError: {
  type: [Boolean, Object],
  default: true
 },
 name: {
  type: String,
  default: 'form'
 },
 layout: {
  type: String,
  default: 'horizontal',
  validator: (value) => ['horizontal', 'vertical', 'inline'].includes(value)
 },
 disabled: {
  type: Boolean,
  default: false
 },
 colon: {
  type: Boolean,
  default: true
 },

 // Submit button configuration
 showSubmitButton: {
  type: Boolean,
  default: true
 },
 submitText: {
  type: String,
  default: 'Submit'
 },
 submitLoading: {
  type: Boolean,
  default: false
 },
 submitWrapperCol: {
  type: Object,
  default: () => ({ span: 18, offset: 6 })
 },

 // Reset button configuration
 showResetButton: {
  type: Boolean,
  default: true
 },
 resetText: {
  type: String,
  default: 'Reset'
 },

 // Form behavior
 validateOnMount: {
  type: Boolean,
  default: false
 },
 showSuccessMessage: {
  type: Boolean,
  default: true
 },
 successMessage: {
  type: String,
  default: 'Form submitted successfully!'
 }
});

const emit = defineEmits([
 'finish',
 'finishFailed',
 'validate',
 'valuesChange',
 'reset'
]);


// Form instance ref (will be set by the form component)
const formRef = ref(null);

// Handle form submission
const handleFinish = (values) => {
 if (props.showSuccessMessage) {
  message.success(props.successMessage);
 }
 emit('finish', values);
};

// Handle form submission failure
const handleFinishFailed = ({ values, errorFields, outOfDate }) => {
 emit('finishFailed', { values, errorFields, outOfDate });
};

// Handle form validation
const handleValidate = (name, status, errorMsgs) => {
 emit('validate', name, status, errorMsgs);
};

// Reset form to initial values
const resetForm = () => {
 Object.keys(formState).forEach(key => {
  delete formState[key];
 });
 Object.assign(formState, cloneDeep(originalValues.value));
 emit('reset');
};

// Validate form on mount if required
onMounted(() => {
 if (props.validateOnMount) {
  // Use nextTick to ensure the form is fully mounted
  nextTick(() => {
   formRef.value?.validate();
  });
 }
});

// Provide form state and reset function to child components
// provide('formState', formState);
provide('resetForm', resetForm);
</script>
<template>
 <a-form
     :model="initialValues"
     :label-col="labelCol"
     :wrapper-col="wrapperCol"
     :rules="rules"
     :validate-trigger="validateTrigger"
     :scroll-to-first-error="scrollToFirstError"
     :name="name"
     :layout="layout"
     :disabled="disabled"
     :colon="colon"
     @finish="handleFinish"
     @finishFailed="handleFinishFailed"
     @validate="handleValidate"
 >
  <!-- Default slot for form content -->
  <slot></slot>

  <a-form-item v-if="showSubmitButton" :wrapper-col="submitWrapperCol">
   <a-space>
    <a-button
        v-if="showSubmitButton"
        type="primary"
        html-type="submit"
        :loading="submitLoading"
    >
     {{ submitText }}
    </a-button>
    <a-button
        v-if="showResetButton"
        html-type="button"
        @click="resetForm"
    >
     {{ resetText }}
    </a-button>
    <slot name="actions"></slot>
   </a-space>
  </a-form-item>
 </a-form>
</template>
<style scoped>
/* You can add custom styles here if needed */
</style>
