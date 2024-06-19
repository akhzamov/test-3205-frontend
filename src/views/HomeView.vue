<script setup lang="ts">
import { Ref, reactive, ref } from "vue";
import axios, { CancelTokenSource } from "axios";
import { vMaska } from 'maska';
import { useForm } from "vee-validate";
import * as yup from "yup";

type TMaskaNumber = {
  masked: string,
  unmasked: string,
  completed: boolean
}

type TResult = {
  email: string,
  number: string,
}

type TError = {
  message: string,
  error: boolean
}

type TSchema = {
  email: string
}

const schema = yup.object({
  email: yup
    .string()
    .email('Введите корректный email: example@gamil.com')
    .required("Введите email"),
});

const { values, handleSubmit, meta, handleReset, defineField, errors } =
  useForm<TSchema>({
    validationSchema: schema,
  });

const [email, emailAttrs] = defineField("email");
const number: Ref<string> = ref('');
const maskedNumber: TMaskaNumber = reactive({
  masked: '',
  unmasked: '',
  completed: false
});
const loader: Ref<boolean> = ref(false);

const results: Ref<TResult[]> = ref([]);
const errorResult: TError = reactive({
  message: '',
  error: false
});

let cancelTokenSource: CancelTokenSource | null = null;

const search = handleSubmit(async (values: TSchema) => {
  if (cancelTokenSource) {
    cancelTokenSource.cancel("Request canceled due to new request.");
  }
  cancelTokenSource = axios.CancelToken.source();

  try {
    loader.value = true;
    results.value = [];
    errorResult.error = false;

    const params: { email: string, number?: string } = {
      email: values.email,
    };

    if (maskedNumber.unmasked) {
      params.number = maskedNumber.unmasked;
    }

    const response = await axios.get("http://localhost:3000/search", {
      params,
      cancelToken: cancelTokenSource.token,
    });
    results.value = response.data;
    loader.value = false;
  } catch (error: any) {
    if (axios.isCancel(error)) {
      console.log("Request canceled:", error.message);
    } else {
      errorResult.message = error.response.data.message;
      errorResult.error = true;
    }
  } finally {
    cancelTokenSource = null;
  }
});

const resendRequest = () => {
  search();
};
</script>

<template>
  <section class="home">
    <form @submit.prevent="search" class="form" v-if="!loader">
      <h3 class="form__title">Search Form</h3>
      <div class="form__input-group">
        <label for="email">Email</label>
        <input type="email" id="email" placeholder="3205@gmail.com" v-model="email" v-bind="emailAttrs">
        <span v-if="errors.email">{{ errors.email }}</span>
      </div>
      <div class="form__input-group">
        <label for="number">Number</label>
        <input type="text" id="number" placeholder="22-22-22" v-model="number" v-maska="maskedNumber"
          data-maska="##-##-##">
      </div>
      <button class="form__button" type="submit">Submit</button>
    </form>
    <div class="form-loader" v-if="loader">
      <div class="loader"></div>
      <button class="form__button" @click="resendRequest" type="button">Resend</button>
    </div>
    <div class="form-result">
      <h3>Results:</h3>
      <ul>
        <li v-for="(result, index) in results" :key="index">
          <p>{{ result.email }} - {{ result.number }}</p>
        </li>
        <p v-if="errorResult.error">{{ errorResult.message }}</p>
      </ul>
    </div>
  </section>
</template>
