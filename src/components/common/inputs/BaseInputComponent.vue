<script setup>
import { computed } from 'vue';

const props = defineProps({
 // Common props
 type: {
  type: String,
  default: 'text',
  validator: (value) => ['text', 'password', 'number', 'textarea'].includes(value)
 },
 modelValue: {
  type: [String, Number],
  default: ''
 },
 placeholder: {
  type: String,
  default: 'Please input'
 },
 disabled: {
  type: Boolean,
  default: false
 },
 size: {
  type: String,
  default: 'middle',
  validator: (value) => ['large', 'middle', 'small'].includes(value)
 },
 status: {
  type: String,
  default: undefined,
  validator: (value) => ['', 'error', 'warning'].includes(value)
 },

 // Form Item props
 name: {
  type: String,
  default: ''
 },
 label: {
  type: String,
  default: ''
 },
 formRules: {
  type: Array,
  default: () => []
 },
 validateStatus: {
  type: String,
  default: '',
  validator: (value) => ['', 'success', 'warning', 'error', 'validating'].includes(value)
 },
 help: {
  type: String,
  default: ''
 },
 extra: {
  type: String,
  default: ''
 },
 required: {
  type: Boolean,
  default: false
 },
 labelCol: {
  type: Object,
  default: null
 },
 wrapperCol: {
  type: Object,
  default: null
 },
 hasFeedback: {
  type: Boolean,
  default: false
 },

 // Text/Password Input props
 maxLength: {
  type: Number,
  default: undefined
 },
 allowClear: {
  type: Boolean,
  default: false
 },
 prefix: {
  type: String,
  default: undefined
 },
 suffix: {
  type: String,
  default: undefined
 },
 addonBefore: {
  type: String,
  default: undefined
 },
 addonAfter: {
  type: String,
  default: undefined
 },

 // Password specific props
 visibilityToggle: {
  type: Boolean,
  default: true
 },

 // Number Input props
 min: {
  type: Number,
  default: undefined
 },
 max: {
  type: Number,
  default: undefined
 },
 step: {
  type: [Number, String],
  default: 1
 },
 precision: {
  type: Number,
  default: undefined
 },
 formatter: {
  type: Function,
  default: undefined
 },
 parser: {
  type: Function,
  default: undefined
 },
 keyboard: {
  type: Boolean,
  default: true
 },
 controls: {
  type: Boolean,
  default: true
 },

 // Textarea props
 autoSize: {
  type: [Boolean, Object],
  default: false
 },
 showCount: {
  type: Boolean,
  default: false
 },

 // Validation props
 validateOnInput: {
  type: Boolean,
  default: false
 },
 rules: {
  type: Array,
  default: () => []
 }
});

const emit = defineEmits([
 'update:modelValue',
 'pressEnter',
 'focus',
 'blur',
 'change',
 'validate'
]);

const inputValue = computed({
 get: () => props.modelValue,
 set: (value) => {
  emit('update:modelValue', value);
  // if (props.validateOnInput) {
  //  validate(value);
  // }
 }
});

const validate = (value) => {
 if (!props.rules.length) return true;

 for (const rule of props.rules) {
  if (rule.required && !value && value !== 0) {
   emit('validate', { valid: false, message: rule.message || 'This field is required' });
   return false;
  }

  if (rule.pattern && !rule.pattern.test(String(value))) {
   emit('validate', { valid: false, message: rule.message || 'Invalid format' });
   return false;
  }

  if (rule.validator && typeof rule.validator === 'function') {
   const result = rule.validator(value);
   if (result !== true) {
    emit('validate', { valid: false, message: result || rule.message || 'Validation failed' });
    return false;
   }
  }
 }

 emit('validate', { valid: true, message: '' });
 return true;
};

const onPressEnter = (e) => {
 emit('pressEnter', e);
};

const onFocus = (e) => {
 emit('focus', e);
};

const onBlur = (e) => {
 emit('blur', e);
 if (!props.validateOnInput) {
  validate(inputValue.value);
 }
};

const onChange = (e) => {
 emit('change', e);
};

// Special handler for InputNumber which directly returns the value, not an event
const onNumberChange = (value) => {
 emit('change', value);
};
</script>
<template>
 <a-form-item
     :name="name"
     :label="label"
     :extra="extra"
     :required="required"
     :label-col="labelCol"
     :wrapper-col="wrapperCol"
 >
  <a-input
      v-if="type === 'text'"
      v-model:value="inputValue"
      :placeholder="placeholder"
      :disabled="disabled"
      :size="size"
      :max-length="maxLength"
      :allow-clear="allowClear"
      :status="status"
      :prefix="prefix"
      :suffix="suffix"
      :addon-before="addonBefore"
      :addon-after="addonAfter"
      @pressEnter="onPressEnter"
      @focus="onFocus"
      @blur="onBlur"
      @change="onChange"
  >
   <template #prefix v-if="$slots.prefix">
    <slot name="prefix"></slot>
   </template>
   <template #suffix v-if="$slots.suffix">
    <slot name="suffix"></slot>
   </template>
   <template #addonBefore v-if="$slots.addonBefore">
    <slot name="addonBefore"></slot>
   </template>
   <template #addonAfter v-if="$slots.addonAfter">
    <slot name="addonAfter"></slot>
   </template>
  </a-input>

  <!-- Password Input -->
  <a-input-password
      v-else-if="type === 'password'"
      v-model:value="inputValue"
      :placeholder="placeholder"
      :disabled="disabled"
      :size="size"
      :max-length="maxLength"
      :allow-clear="allowClear"
      :status="status"
      :prefix="prefix"
      :visibilityToggle="visibilityToggle"
      @pressEnter="onPressEnter"
      @focus="onFocus"
      @blur="onBlur"
      @change="onChange"
  >
   <template #prefix v-if="$slots.prefix">
    <slot name="prefix"></slot>
   </template>
  </a-input-password>

  <!-- Number Input -->
  <a-input-number
      v-else-if="type === 'number'"
      v-model:value="inputValue"
      :placeholder="placeholder"
      :disabled="disabled"
      :size="size"
      :min="min"
      :max="max"
      :step="step"
      :precision="precision"
      :formatter="formatter"
      :parser="parser"
      :keyboard="keyboard"
      :status="status"
      :controls="controls"
      :addon-before="addonBefore"
      :addon-after="addonAfter"
      @pressEnter="onPressEnter"
      @focus="onFocus"
      @blur="onBlur"
      @change="onNumberChange"
  />

  <!-- Textarea Input -->
  <a-textarea
      v-else-if="type === 'textarea'"
      v-model:value="inputValue"
      :placeholder="placeholder"
      :disabled="disabled"
      :size="size"
      :max-length="maxLength"
      :allow-clear="allowClear"
      :auto-size="autoSize"
      :show-count="showCount"
      :status="status"
      @pressEnter="onPressEnter"
      @focus="onFocus"
      @blur="onBlur"
      @change="onChange"
  />

  <!-- Default to standard input if type is not recognized -->
  <a-input
      v-else
      v-model:value="inputValue"
      :placeholder="placeholder"
      :disabled="disabled"
      :size="size"
      :max-length="maxLength"
      :allow-clear="allowClear"
      :status="status"
      :prefix="prefix"
      :suffix="suffix"
      :addon-before="addonBefore"
      :addon-after="addonAfter"
      @pressEnter="onPressEnter"
      @focus="onFocus"
      @blur="onBlur"
      @change="onChange"
  >
   <template #prefix v-if="$slots.prefix">
    <slot name="prefix"></slot>
   </template>
   <template #suffix v-if="$slots.suffix">
    <slot name="suffix"></slot>
   </template>
   <template #addonBefore v-if="$slots.addonBefore">
    <slot name="addonBefore"></slot>
   </template>
   <template #addonAfter v-if="$slots.addonAfter">
    <slot name="addonAfter"></slot>
   </template>
  </a-input>
 </a-form-item>
</template>
<style scoped>
/* You can add custom styles here if needed */
</style>
