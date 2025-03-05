<script setup>
import { ref, computed, watch } from 'vue';

const number_to_convert = ref('');
const result = ref(null);
const convert_from = ref('');
const convert_to = ref('');

const optionsChosen = computed(() => {
    return convert_from.value === '' || convert_to.value === '';
})

const isNumberValid = computed(() => {
    switch (convert_from.value) {
        case 'Decimal':
            return number_to_convert.value === '' || /^[+]?\d+$/.test(number_to_convert.value);
        case 'Binary':
            return number_to_convert.value === '' || /^[01]+$/.test(number_to_convert.value);
        case 'Hexadecimal':
            return number_to_convert.value === '' || /[0-9A-Fa-f]+$/.test(number_to_convert.value);
        case 'Octal':
            return number_to_convert.value === '' || /^[0-7]+$/.test(number_to_convert.value)
    }
})

const sameSystem = computed(() => {
    return convert_from.value === convert_to.value && (convert_from.value !== '' && convert_to.value !== '');
})

function swap() {
    let temp = convert_from.value;
    convert_from.value = convert_to.value;
    convert_to.value = temp;
}

function conversion() {
    let base = 0;
    switch (convert_from.value) {
        case 'Decimal': base = 10; break;
        case 'Binary': base = 2; break;
        case 'Hexadecimal': base = 16; break;
        case 'Octal': base = 8; break;
    }

    let dec = parseInt(number_to_convert.value, base);

    switch (convert_to.value) {
        case 'Decimal':
            result.value = dec.toString(10);
            break;
        case 'Binary':
            result.value = dec.toString(2);
            break;
        case 'Hexadecimal':
            result.value = dec.toString(16).toUpperCase();
            break;
        case 'Octal':
            result.value = dec.toString(8);
            break;
    }
}

watch([number_to_convert, convert_to, convert_from], () => {
  result.value = null;
});

</script>

<template>
    <div class="col-12 col-sm-12 col-md-10 col-lg-8 col-xl-6">    
        <div class="row g-2 my-2">
            <div class="col-12 col-md-5">
                <select v-model="convert_from" class="form-select" :class="{'is-invalid': sameSystem}" placeholder="Convert from:">
                    <option disabled value="">Choose numeric system:</option>
                    <option>Decimal</option>
                    <option>Binary</option>
                    <option>Hexadecimal</option>
                    <option>Octal</option>
                </select>
            </div>
            <div class="col-12 col-md-2 d-flex justify-content-center align-items-md-center">
                <button @click="swap" class="btn border-0" :disabled="optionsChosen || sameSystem">
                    <img src="/src/assets/arrow-left-right.svg" alt="swap" width="20" height="20">
                </button>
            </div>
            <div class="col-12 col-md-5">
                <select v-model="convert_to" class="form-select" :class="{'is-invalid': sameSystem}" placeholder="Convert to:">
                    <option disabled value="">Choose numeric system:</option>
                    <option>Decimal</option>
                    <option>Binary</option>
                    <option>Hexadecimal</option>
                    <option>Octal</option>
                </select>
            </div>
            <div class="col-12">
                <p v-if="sameSystem" class="text-center text-danger">Choose different numeric systems.</p>
            </div>
        </div>
        <div class="my-2">
            <input v-model="number_to_convert" :disabled="optionsChosen || sameSystem" class="form-control" :class="{'is-invalid': !isNumberValid && !optionsChosen}" type="text" placeholder="Enter number...">
            <p v-if="!isNumberValid && !optionsChosen" class="invalid-feedback">Number does not exist in {{convert_from.toLowerCase()}} system.</p>
        </div>
        <div class="my-2">
            <button @click="conversion" class="btn btn-dark w-100">Convert</button>
        </div>
        <div v-if="result !== null" class="my-3">
            <h5 class="text-xl text-center text-break">{{convert_from}} <span class="fw-bold">{{ number_to_convert }}</span> to {{ convert_to.toLowerCase() }} is: <span class="fw-bold">{{ result }}</span></h5>
        </div>
    </div>
</template>
