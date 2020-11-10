<template>
  <div class="form-holder">
    <form class="form" @submit.prevent="onSubmit" ref="form">
      <div class="form__group">
        <span class="form__input-title">Фамиля*</span>
        <input class="form__input"
               v-model.trim='$v.client.lastName.$model'
               placeholder="Павлов"
               :class="{ 'form--input-invalid': this.$v.client.lastName.$error }"
        >
        <span class="form__input-title">Имя*</span>
        <input class="form__input"
               id="firstName"
               v-model.trim='$v.client.firstName.$model'
               placeholder="Иван"
               :class="{ 'form--input-invalid': this.$v.client.firstName.$error }"
        >
        <span class="form__input-title">Отчество</span>
        <input class="form__input"
               v-model='client.patronymic'
               placeholder="Петрович"
        >
        <span class="form__input-title">Дата рождения*</span>
        <input class="form__input"
               @input="validateDate(`birthDate`)"
               type="date"
               v-model.trim='$v.client.birthDate.$model'
               :class="{ 'form--input-invalid': this.$v.client.birthDate.$error }"
        >
        <span class="form__input-title">Номер телефона</span>
        <input class="form__input"
               type="tel"
               v-model.trim='$v.client.phoneNumber.$model'
               @input="onPhoneNumberChange"
               :class="{ 'form--input-invalid': this.$v.client.phoneNumber.$error }"
        >
        <small
          class="form__invalid-message"
          v-if="!this.$v.client.phoneNumber.numeric"
        >
          Номер должен состоять только из цифр
        </small>
        <small
          class="form__invalid-message"
          v-else-if="(!this.$v.client.phoneNumber.maxLength || !this.$v.client.phoneNumber.minLength)
          && this.$v.client.phoneNumber.$dirty"
        >
          Номер должен состоять из 11-и символов, у вас {{this.client.phoneNumber.length}}
        </small>
        <span class="form__input-title">Пол</span>
        <div class="form__input">
          <input type="radio" value="М" v-model="client.gender">
          <label>М</label>
          <input type="radio" value="Ж" v-model="client.gender">
          <label>Ж</label>
        </div>
        <span class="form__input-title">Категория пациентов</span>
        <select class="form__input"
                multiple
                v-model="$v.client.category.$model"
                :class="{ 'form--input-invalid': this.$v.client.category.$error }"
        >
          <option>ОМС</option>
          <option>VIP</option>
          <option>Проблемные</option>
        </select>
        <span class="form__input-title">Лечащий врач</span>
        <select class="form__input" v-model="client.doctor">
          <option v-for="doctor in doctors" :key="doctor.name">
            {{ doctor.name }}
          </option>
        </select>
      </div>

      <div class="form__group">
        <span class="form__input-title">Почтовый индекс</span>
        <input class="form__input" v-model="client.postIndex" type="number" placeholder="270015">
        <span class="form__input-title">Страна</span>
        <input class="form__input" v-model="client.country" placeholder="Россия">
        <span class="form__input-title">Область</span>
        <input class="form__input" v-model="client.region" placeholder="Иркутская">
        <span class="form__input-title">Город*</span>
        <input class="form__input"
               placeholder="Иркутск"
               v-model.trim='$v.client.city.$model'
               :class="{ 'form--input-invalid': this.$v.client.city.$error }"
        >
        <span class="form__input-title">Улица</span>
        <input class="form__input" v-model="client.street" placeholder="Московская">
        <span class="form__input-title">Дом</span>
        <input class="form__input" v-model="client.house" placeholder="12-5">
      </div>

      <div class="form__group">
        <span class="form__input-title">Тип документа*</span>
        <select class="form__input"
                v-model="$v.client.documentType.$model"
                :class="{ 'form--input-invalid': this.$v.client.documentType.$error }"
        >
          <option>Паспорт</option>
          <option>Свидетельство</option>
          <option>Права</option>
        </select>
        <span class="form__input-title">Серия документа</span>
        <input class="form__input" type="number" v-model="client.series" placeholder="BM">
        <span class="form__input-title">Номер документа</span>
        <input class="form__input" type="number" v-model="client.number" placeholder="3848623">
        <span class="form__input-title">Выдавший орган</span>
        <input class="form__input" v-model="client.issuedBy" placeholder="Октябрьское РОВД">
        <span class="form__input-title">Дата выдачи*</span>
        <input class="form__input"
               type="date"
               @input="validateDate(`dateOfIssue`)"
               v-model="$v.client.dateOfIssue.$model"
               placeholder="Когда выдан"
               :class="{ 'form--input-invalid': this.$v.client.dateOfIssue.$error }"
        >
      </div>

      <div class="form__group">
        <div>
          <input class="form__input form__checkbox-input"
                 type="checkbox"
                 v-model="client.forbidMessage"
          > Не отправлять СМС
        </div>
        <span class="form__invalid-message" v-if="this.$v.$error">Заполните все обязательные(*) поля</span>
        <button v-if='!showSuccessMessage' type="submit" class="form__submit-button">подтвердить</button>
      </div>
      <div v-if="showSuccessMessage" class="form__successMessage">
        <span class="form__successMessage-text">Клиент зарегестрирован</span>
      </div>
    </form>
  </div>
</template>

<script>
  import {minLength, maxLength, required, numeric} from 'vuelidate/lib/validators'

  export default {
    name: 'app',
    data() {
      return {
        doctors: [
          {name: 'Иванов', specialisation: 'ophthalmologist'},
          {name: 'Захаров', specialisation: 'cardiologist'},
          {name: 'Чернышева', specialisation: 'thanatologist'}
        ],
        showSuccessMessage: false,
        client: {
          lastName: '',
          firstName: '',
          patronymic: '',
          birthDate: '',
          phoneNumber: '7',
          gender: 'М',
          category: [],
          doctor: '',
          forbidMessage: false,

          postIndex: '',
          country: '',
          region: '',
          city: '',
          street: '',
          house: '',

          documentType: '',
          series: '',
          number: '',
          issuedBy: '',
          dateOfIssue: ''
        },
        clients: []
      }
    },
    validations: {
      client: {
        lastName: {required},
        firstName: {required},
        birthDate: {required},
        phoneNumber: {required, minLength: minLength(11), maxLength: maxLength(11), numeric},
        category: {required},
        city: {required},
        documentType: {required},
        dateOfIssue: {required}
      }
    },
    methods: {
      onSubmit(event) {
        if (this.$v.$invalid) {
          this.$v.$touch()
          return
        }
        this.clients.push(this.client)
        this.showSuccessMessage = true
        setTimeout(() => {
          this.showSuccessMessage = false
          event.target.reset()
        }, 2000)
      },
      onPhoneNumberChange() {
        let newText = this.client.phoneNumber.split('')
        newText[0] = '7'
        this.client.phoneNumber = newText.join('')
      },
      validateDate(targetValue) {
        let date = new Date
        if (Number(this.client[`${targetValue}`].split('-')[0]) > 2020) {
          let incorrectValue = this.client[`${targetValue}`].split('-')
          incorrectValue[0] = String(date.getFullYear())
          this.client[`${targetValue}`] = incorrectValue.join('-')
        }
      }
    }
  }
</script>

<style lang="sass">
    $mainColor: rgba(10, 140, 199, 0.9)
    $errorColor: rgba(200, 0, 17, 0.9)
    $buttonColor: rgba(10, 140, 199, 0.3)

    .form-holder
      margin: 0 auto
      max-width: 360px

    .form
      display: flex
      flex-direction: column

    .form__group
      display: flex
      flex-direction: column
      margin-bottom: 10px

    .form__input
      margin-bottom: 5px
      border: solid $mainColor 2px
      border-radius: 10px
      padding-left: 5px
      outline: 0

    .form__input:focus
      box-shadow: $mainColor 0 0 10px

    .form__input-title
      color: $mainColor

    .form__submit-button
      border-radius: 10px
      border: $buttonColor solid 2px
      background-color: $buttonColor
      margin-top: 5px
      outline: 0
      max-width: 300px

    .form__submit-button:active
      box-shadow: $buttonColor 0 0 10px

    .form--input-invalid
      border: solid $errorColor 2px
      box-shadow: $errorColor 0 0 5px

    .form__invalid-message
      color: $errorColor

    .form__successMessage
      text-align: center
      background-color: lightgreen
      border-radius: 10px
      padding: 10px

</style>
