<template>
  <div class="product-form-container">
    <div class="product-form-tab">
      <h1>Title</h1>
      <p>Total Cost: £{{total}}</p>
    </div>
    <form @submit="formSubmit" class="product-form">
      <div>
        <select class="select-service" v-model="service" @change="formTabColour">
          <option>Service 1</option>
          <option>Service 2</option>
          <option>Service 3</option>
        </select>
      </div>
      <div class="form-group" :class="{ 'form-group--error': $v.quantity.$error }">
        <label class="form__label">Quantity</label>
        <input
          class="form__input"
          v-model.trim="quantity"
          @input="setQuantity($event.target.value)"
        >
      </div>
      <div class="error" v-if="!$v.quantity.required">Field is required</div>
      <div
        class="error"
        v-if="!$v.quantity.between"
      >Must be between {{$v.quantity.$params.between.min}} and {{$v.quantity.$params.between.max}}</div>
      <div class="error" v-if="!$v.quantity.numeric">Must be a number</div>
      <div class="form-group" :class="{ 'form-group--error': $v.quantity.$error }">
        <label class="form__label">Unit Cost (£)</label>
        <input
          class="form__input"
          v-model.trim="unitCost"
          @input="setUnitCost($event.target.value)"
        >
      </div>
      <div class="error" v-if="!$v.unitCost.required">Field is required</div>
      <div class="error" v-if="!$v.unitCost.numeric">Must be a number</div>
      <div class="form-group">
        <label class="form__label">Total Cost</label>
        <div class="form-total-container">
          <p class="form-total">£{{total}}</p>
        </div>
      </div>
      <div>
        <button class="button" type="submit" :disabled="submitStatus === 'PENDING'">Submit</button>
        <p class="typo__p" v-if="submitStatus === 'OK'">Thanks for your submission!</p>
        <p class="typo__p" v-if="submitStatus === 'ERROR'">Please fill the form correctly.</p>
        <p class="typo__p" v-if="submitStatus === 'PENDING'">Sending...</p>
      </div>
    </form>
  </div>
</template>

<script>
import { required, between, numeric } from "vuelidate/lib/validators";

export default {
  name: "app",
  components: {},
  data() {
    return {
      service: "Service 1",
      quantity: "",
      unitCost: "",
      total: "",
      submitStatus: null
    };
  },
  validations: {
    quantity: {
      required,
      between: between(1, 100),
      numeric
    },
    unitCost: {
      required,
      numeric
    }
  },
  mounted() {
    this.formTabColour();
  },
  methods: {
    setQuantity(value) {
      this.quantity = value;
      this.$v.quantity.$touch();
    },
    setUnitCost(value) {
      this.unitCost = value;
      this.$v.unitCost.$touch();
    },
    formSubmit(e) {
      e.preventDefault();

      // Form failed submission
      this.$v.$touch();
      if (this.$v.$invalid) {
        this.submitStatus = "ERROR";
      } else {
        // do your submit logic here
        this.submitStatus = "PENDING";
        setTimeout(() => {
          this.submitStatus = "OK";
          // Form completed submission
          console.log("Form Submitted");
          this.calcUnitCost();

          const formData = {
            service: this.service,
            quantity: this.quantity,
            unitCost: `£${this.unitCost}`,
            totalCost: `£${this.total}`
          };

          console.log(formData);

          // Clear form fields
          this.service = this.service;
          this.quantity = "";
          this.unitCost = "";
          this.totalCost = "";
        }, 500);
      }
    },
    calcUnitCost() {
      this.total = this.unitCost * this.quantity;
    },
    formTabColour() {
      if (this.service === "Service 1") {
        this.removeTabClass();
        document.querySelector(".product-form-tab").classList.add("service-1");
      } else if (this.service === "Service 2") {
        this.removeTabClass();
        document.querySelector(".product-form-tab").classList.add("service-2");
      } else if (this.service === "Service 3") {
        this.removeTabClass();
        document.querySelector(".product-form-tab").classList.add("service-3");
      } else {
        this.removeTabClass();
      }
    },
    removeTabClass() {
      document.querySelector(".product-form-tab").classList.remove("service-1");
      document.querySelector(".product-form-tab").classList.remove("service-2");
      document.querySelector(".product-form-tab").classList.remove("service-3");
      // document.querySelector(".error").style.display = "none";
    }
  }
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css?family=Source+Sans+Pro:400,700");
:root {
  --primary-colour: #343436;
}
html {
  font-size: 62.5%; /* font-size 1em = 10px on default browser settings */
}
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-family: "Source Sans Pro", sans-serif;
  font-size: 1.8rem;
  display: grid;
  justify-content: center;
  background: #0e7de6;
}
.product-form-container {
  display: grid;
  max-width: 1200px;
  background: #f7f7fc;
  -webkit-box-shadow: 15px 14px 38px -20px rgba(69, 69, 69, 1);
  -moz-box-shadow: 15px 14px 38px -20px rgba(69, 69, 69, 1);
  box-shadow: 15px 14px 38px -20px rgba(69, 69, 69, 1);
}
.product-form-tab {
  display: grid;
  grid-template-columns: 1fr 1fr;
  align-content: center;
  width: 31.25rem;
  padding: 0.625rem;
  height: 5rem;
  transform: skew(20deg);
  /* background: #222d40; */
  color: #ffffff;

  h1 {
    text-transform: uppercase;
  }
}
.service-1 {
  background: #222d40;
}
.service-2 {
  background: #99a0ef;
}
.service-3 {
  background: #ec9556;
}
.select-service {
  width: 100%;
  font-size: 1.5rem;
}
.form__label {
  margin: 1rem 0 1rem 0;
}
.form-group {
  display: grid;
  grid-template-columns: 1fr;
}
.form__input {
  height: 2.5rem;
  font-size: 1.6rem;
  padding: 1.4rem;
}
.product-form {
  padding: 3rem;
}
.error {
  color: rgb(161, 19, 19);
}
.typo__p {
  color: rgb(161, 19, 19);
}
.form-total-container {
  border-bottom: 2px solid black;
  margin-bottom: 2rem;
  p {
    font-size: 3rem;
  }
}
.button {
  background: #0e7de6;
  padding: 2rem;
  color: #ffffff;
  text-align: center;
  font-size: 1.5rem;
  width: 100%;
  cursor: pointer;
}
</style>
