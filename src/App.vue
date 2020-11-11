<template>
  <div class="form-holder">
    <form class="form" @submit.prevent="onSubmit" ref="form">
      <div class="form__group">
        <span class="form__input-title">Фамиля*</span>
        <div class="form__input-holder">
          <input class="form__input form__alpha-input"
                 v-model.trim='$v.client.lastName.$model'
                 placeholder="Павлов"
                 :class="{ 'form--input-invalid': this.$v.client.lastName.$error }"
          >
          <small
            class="form__invalid-message"
            v-if="!this.$v.client.lastName.rusAlpha"
          >
            Допускаются только буквы одного алфавита
          </small>
        </div>
        <span class="form__input-title">Имя*</span>
        <div class="form__input-holder">
          <input class="form__input form__alpha-input"
                 id="firstName"
                 v-model.trim='$v.client.firstName.$model'
                 placeholder="Иван"
                 :class="{ 'form--input-invalid': this.$v.client.firstName.$error }"
          >

          <small
            class="form__invalid-message"
            v-if="!this.$v.client.firstName.rusAlpha"
          >
            Допускаются только буквы одного алфавита
          </small>
        </div>
        <span class="form__input-title">Отчество</span>
        <div class="form__input-holder">
          <input class="form__input form__alpha-input"
                 v-model.trim='$v.client.patronymic.$model'
                 placeholder="Петрович"
                 :class="{ 'form--input-invalid': this.$v.client.patronymic.$error }"
          >

          <small
            class="form__invalid-message"
            v-if="!this.$v.client.patronymic.rusAlpha"
          >
            Допускаются только буквы одного алфавита
          </small>
        </div>
        <span class="form__input-title">Дата рождения*</span>
        <input class="form__input"
               @input="validateDate(`birthDate`)"
               type="date"
               v-model.trim='$v.client.birthDate.$model'
               :class="{ 'form--input-invalid': this.$v.client.birthDate.$error }"
        >
        <span class="form__input-title">Номер телефона</span>
        <div class='form__input-holder'>
          <span class="form__phone-input-fixedNumber">+7</span>
          <input class="form__input form__phone-input"
                 type="tel"
                 v-model.trim='$v.client.phoneNumber.$model'
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
            Номер должен состоять из 10-и символов, у вас {{this.client.phoneNumber.length}}
          </small>
        </div>
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
        <div class="form__input-holder">
          <input class="form__input form__alpha-input"
                 v-model.trim='$v.client.country.$model'
                 placeholder="Россия"
                 :class="{ 'form--input-invalid': this.$v.client.country.$error }"
          >
          <small
            class="form__invalid-message"
            v-if="!this.$v.client.country.rusAlpha"
          >
            Допускаются только буквы одного алфавита
          </small>
        </div>
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
        <input class="form__input" v-model="client.series" placeholder="BM">
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

  const rusAlpha = (value) => {
    return /^[а-яё]*$/i.test(value) || /^[a-z]*$/i.test(value);
  }

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
          phoneNumber: '',
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
        lastName: {required, rusAlpha},
        firstName: {required, rusAlpha},
        patronymic: {rusAlpha},
        birthDate: {required},
        phoneNumber: {required, minLength: minLength(10), maxLength: maxLength(10), numeric},
        category: {required},
        city: {required},
        documentType: {required},
        dateOfIssue: {required},
        country: {rusAlpha}
      }
    },
    methods: {
      fixPhoneNumber() {
        let correctedNumber = this.client.phoneNumber.split('');
        correctedNumber.unshift('7');
        this.client.phoneNumber = correctedNumber.join('');
      },
      onSubmit(event) {
        if (this.$v.$invalid) {
          this.$v.$touch();
          return;
        }
        this.fixPhoneNumber();
        this.clients.push(this.client);
        this.showSuccessMessage = true;
        setTimeout(() => {
          this.showSuccessMessage = false;
          event.target.reset();
        }, 2000)
        let phoneNumber = this.client.phoneNumber.split('')
        phoneNumber.shift()
        this.client.phoneNumber = phoneNumber.join('')
      },
      validateDate(targetValue) {
        let date = new Date;
        let value = this.client[`${targetValue}`].split('-');
        const year = date.getFullYear();
        const month = date.getMonth();
        const day = date.getDate();
        if (
          Number(value[0]) > year ||
          (Number(value[0]) === year && Number(value[1]) > (month + 1)) ||
          (Number(value[0]) === year && Number(value[1]) === (month + 1) && Number(value[2]) > day)
        ) {
          let incorrectValue = value;
          incorrectValue[0] = String(year);
          incorrectValue[1] = String((month + 1));
          incorrectValue[2] = String(day);
          this.client[`${targetValue}`] = incorrectValue.join('-');
        }
      }
    }
  }
</script>

<style lang="sass">

  $borderColor: rgba(0, 0, 0, 0.6)
  $mainColor: rgba(0, 0, 0, 0.9)
  $errorColor: rgba(200, 0, 17, 0.9)
  $buttonColor: rgba(0, 0, 0, 0.1)
  $successColor: rgba(100, 255, 127, 0.7)

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
    margin-bottom: 15px
    margin-top: 5px
    border: solid $borderColor 2px
    border-radius: 7px
    padding: 5px
    outline: 0

  .form__input:focus
    box-shadow: $mainColor 0 0 3px
    border: solid $mainColor 2px

  .form__input-title
    color: $mainColor

  .form__input-holder
    margin-bottom: 15px
    display: flex
    flex-direction: column

  .form__phone-input-fixedNumber
    position: absolute
    padding-top: 11.5px
    padding-left: 7px
    font-size: 14px

  .form__phone-input
    padding-left: 22px
    margin-bottom: 0

  .form__alpha-input
    margin-bottom: 0

  .form__submit-button
    border-radius: 10px
    padding: 5px
    font-size: 16px
    border: $buttonColor solid 2px
    background-color: $buttonColor
    margin-top: 5px
    outline: 0
    max-width: 200px

  .form__submit-button:active
    box-shadow: $buttonColor 0 0 8px

  .form--input-invalid
    border: solid $errorColor 2px
    box-shadow: $errorColor 0 0 2px

  .form__invalid-message
    margin-top: 5px
    color: $errorColor

  .form__successMessage
    text-align: center
    background-color: $successColor
    border-radius: 10px
    padding: 10px

</style>
