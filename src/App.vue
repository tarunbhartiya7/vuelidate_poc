<template>
    <div>
        <h1>Form Validation</h1>
        <form @submit.prevent="submitForm" autocomplete="off">
            <div class="form-group">
                <label for="name">Name*:</label>
                <!-- <input
                    :class="{error: $v.form.name.$error, valid: !$v.form.name.$invalid}"
                    v-model="$v.form.name.$model" 
                    id="name"> -->
                <input 
                    :class="{error: shouldAppendErrorClass($v.form.name), valid: shouldAppendValidClass($v.form.name)}"
                    @blur="$v.form.name.$touch()"
                    v-model="form.name" 
                    id="name">
                <p v-if="$v.form.name.$error" class="error-message">The name field is required</p>
                <p>Invalid: {{ $v.form.name.$invalid }} Dirty {{ $v.form.name.$dirty }} Error {{ $v.form.name.$error }}</p>
                <!-- $error = $invalid && $dirty -->
            </div>

            <div class="form-group">
                <label for="age">Age*:</label>
                <input 
                    :class="{error: shouldAppendErrorClass($v.form.age), valid: shouldAppendValidClass($v.form.age)}"
                    v-model.number="form.age" 
                    @blur="$v.form.age.$touch()"
                    id="age">
                <div v-if="$v.form.age.$error">
                    <p v-if="!$v.form.age.required" class="error-message">The age field is required</p>
                    <p v-else-if="!$v.form.age.integer" class="error-message">The age field should be an integer</p>
                    <p v-else-if="!$v.form.age.between" class="error-message">You should be atleast 12 and younger than 120 to continue</p>
                </div>
            </div>

            <div class="form-group">
                <label for="age">Subscribe to the newsletter:</label>
                <input 
                    v-model="form.newsletter" 
                    type="checkbox"
                    @input="$v.form.email.$touch()"
                    id="newsletter">
            </div>

            <div class="form-group">
                <label for="age">Email:</label>
                <input 
                    :class="{error: shouldAppendErrorClass($v.form.email), valid: shouldAppendValidClass($v.form.email)}"
                    v-model="form.email" 
                    @blur="$v.form.email.$touch()"
                    id="email">
                <p v-if="!$v.form.email.required" class="error-message">Email is required so we can send you the newsletter</p>
                <p v-else-if="$v.form.email.$error" class="error-message">Invalid email address</p>
            </div>


            <div class="form-group">
                <label for="age">Pizza or Burger:</label>
                <input 
                    :class="{error: shouldAppendErrorClass($v.form.food), valid: shouldAppendValidClass($v.form.food)}"
                    v-model="form.food" 
                    @blur="$v.form.food.$touch()"
                    id="food">
                <p v-if="$v.form.food.$error" class="error-message">We only serve Pizzas and burgers.</p>
            </div>

            <div>
                <!-- <button :disabled="$v.form.$invalid">Submit</button> -->
                <button>Submit</button>
            </div>
        </form>
    </div>
</template>

<script>
    import { required, integer, between, email, helpers, requiredIf } from 'vuelidate/lib/validators'
    const pizzaOrBurger = value => value === 'pizza' || value === 'burger' || !helpers.req(value)
    const ageLimit = between(12, 120)
    export default {
        data() {
            return {
                form: {
                    name: null,
                    age: null,
                    email: null,
                    food: null,
                    newsletter: null
                }
            }
        },
        validations: {
            form: {
                name: {
                    required
                },
                age: {
                    required,
                    integer,
                    ageLimit
                    // min: validators.minValue(12),
                    // max: validators.maxValue(12),
                },
                email: {
                    email,
                    required: requiredIf(function() {
                        // don't use arrow function above
                        return !!this.form.newsletter
                    })
                },
                food: {
                    pizzaOrBurger
                }
            }
        },
        // computed: {
        //     nameIsValid() {
        //         return !!this.form.name
        //     },
        //     ageIsValid() {
        //         return typeof this.form.age === 'number' && this.form.age > 20 && this.form.age < 100
        //     },
        //     formIsValid() {
        //         return this.nameIsValid && this.ageIsValid
        //     }
        // },
        methods: {
            // validation helpers - in a project should export these in a mixin
            shouldAppendValidClass(field) { // ex: field = $v.form.email
                return !field.$invalid && field.$model
            },
            shouldAppendErrorClass(field) { // ex: field = $v.form.email
                return field.$error
            },
            submitForm() {
                this.$v.form.$touch()
                if (!this.$v.form.$invalid) {
                    console.log('📝 Form Submitted', this.form)
                } else {
                    console.log('❌ Invalid form')
                }
            }
        }
    }
</script>

<style>

</style>
