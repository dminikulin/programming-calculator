<script setup>
import { ref, computed, watch } from 'vue';

const inputNumber = ref('');
const result = ref(null);
const convertFrom = ref('');
const convertTo = ref('');
const convertedFrom = ref('');
const convertedTo = ref('');
const convertedInput = ref('');

const SYSTEMS = {
  'Decimal': 10,
  'Binary': 2,
  'Hexadecimal': 16,
  'Octal': 8
};

const areOptionsMissing = computed(() => {
    return convertFrom.value === '' || convertTo.value === '';
})

const isNumberValid = computed(() => {
    switch (convertFrom.value) {
        case 'Decimal':
            return inputNumber.value === '' || /^[+-]?\d+$/.test(inputNumber.value);
        case 'Binary':
            return inputNumber.value === '' || /^[01]+$/.test(inputNumber.value);
        case 'Hexadecimal':
            return inputNumber.value === '' || /^[0-9A-Fa-f]+$/.test(inputNumber.value);
        case 'Octal':
            return inputNumber.value === '' || /^[0-7]+$/.test(inputNumber.value)
    }
})

const sameSystem = computed(() => {
    return convertFrom.value === convertTo.value && (convertFrom.value !== '' && convertTo.value !== '');
})

function swap() {
    let temp = convertFrom.value;
    convertFrom.value = convertTo.value;
    convertTo.value = temp;
}

function conversion() {
    if (!isNumberValid.value || areOptionsMissing.value || sameSystem.value) return;

    const baseFrom = SYSTEMS[convertFrom.value]
    const baseTo = SYSTEMS[convertTo.value];

    const dec = parseInt(inputNumber.value, baseFrom);

    convertedInput.value = inputNumber.value;
    convertedFrom.value = convertFrom.value;
    convertedTo.value = convertTo.value;

    result.value = convertTo.value === 'Hexadecimal'
        ? dec.toString(baseTo).toUpperCase()
        : dec.toString(baseTo);
}

</script>

<template>
    <div class="col-12 col-sm-12 col-md-10 col-lg-8 col-xl-6">    
        <div class="row g-2 my-2">
            <div class="col-12 col-md-5">
                <select v-model="convertFrom"  class="form-select" :class="{'is-invalid': sameSystem}" placeholder="Convert from:">
                    <option disabled value="">Choose numeric system:</option>
                    <option v-for="obj in Object.keys(SYSTEMS)" :key="obj">{{obj}}</option>
                </select>
            </div>
            <div class="col-12 col-md-2 d-flex justify-content-center align-items-md-center">
                <button @click="swap" class="btn border-0" :disabled="areOptionsMissing || sameSystem">
                    <img src="/src/assets/arrow-left-right.svg" alt="swap" width="20" height="20">
                </button>
            </div>
            <div class="col-12 col-md-5">
                <select v-model="convertTo" class="form-select" :class="{'is-invalid': sameSystem}" placeholder="Convert to:">
                    <option disabled value="">Choose numeric system:</option>
                    <option v-for="obj in Object.keys(SYSTEMS)" :key="obj">{{obj}}</option>
                </select>
            </div>
            <div class="col-12">
                <p v-if="sameSystem" class="text-center text-danger">Choose different numeric systems.</p>
            </div>
        </div>
        <div class="my-2">
            <input v-model="inputNumber" :disabled="areOptionsMissing || sameSystem" class="form-control" :class="{'is-invalid': !isNumberValid && !areOptionsMissing}" type="text" placeholder="Enter number...">
            <p v-if="!isNumberValid && !areOptionsMissing" class="invalid-feedback">Number does not exist in {{convertFrom.toLowerCase()}} system.</p>
        </div>
        <div class="my-2">
            <button @click="conversion" class="btn btn-dark w-100" :disabled="!isNumberValid || areOptionsMissing || sameSystem">Convert</button>
        </div>
        <div v-if="result !== null" class="my-3">
            <h5 class="text-xl text-center text-break">{{convertedFrom}} <span class="fw-bold">{{ convertedInput }}</span> to {{ convertedTo.toLowerCase() }} is: <span class="fw-bold">{{ result }}</span></h5>
        </div>
    </div>
</template>
