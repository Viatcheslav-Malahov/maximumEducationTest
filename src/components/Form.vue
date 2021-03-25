<template>
  <section>
    <form action="https://60254fac36244d001797bfe8.mockapi.io/api/v1/send-form" 
    method="POST"
    id="mainForm"></form>
    <p class="branch">Ваш филиал<span>*</span></p>

    <!-- список городов -->
    <div class="cityList">
      <select
        form="mainForm"
        ref="citySelect"
        v-model="selected"
        class="select_grey"
        @change="colorChange(), inputChange()"
      >
        <option class="choose_opt" selected disabled>Выберите город</option>
        <option
          class="city_opt"
          v-for="option in options"
          v-bind:key="option.value"
        >
          {{ option.text }}
        </option>
      </select>
    </div>

    <!-- чекбокс -->
    <div class="cityCheck">
      <input
        form="mainForm"
        type="checkbox"
        id="checkbox"
        v-model="checked"
        @input="cityBlock()"
        @change="inputChange()"
        />
      <label for="checkbox">Онлайн</label>
    </div>

    <!-- Тема обращения -->
    <div class="radio__wrapper">
      <!-- Радио кнопки -->
      <p class="branch">Тема обращения<span>*</span></p>
      <div>
        <input
          @input="inputChange()"
          @change="otherClear(),inputChange()"
          form="mainForm"
          type="radio"
          id="one"
          value="1"
          v-model="picked"
        />
        <label for="one">Недовлен качеством услуг</label>
        <br />
        <input
          @input="inputChange()"
          @change="otherClear(),inputChange()"
          form="mainForm"
          type="radio"
          id="two"
          value="2"
          v-model="picked"
        />
        <label for="two">Расторжение договора</label>
        <br />
        <input
          @input="inputChange()"
          @change="otherClear(),inputChange()"
          form="mainForm"
          type="radio"
          id="three"
          value="3"
          v-model="picked"
        />
        <label for="three">Не приходит письмо активации на почту</label>
        <br />
        <input
          @input="inputChange()"
          @change="otherClear(),inputChange()"
          form="mainForm"
          type="radio"
          id="for"
          value="4"
          v-model="picked"
        />
        <label for="for">Не работает личный кабинет</label>
        <br />
        
      </div>
      <!-- Другое -->
      <div class="other__wrapper">
          <input
            v-model="radiomassage"
            @input="radioChange(),inputChange()"
            ref="radioMSG"
            type="text"
            name=""
            id="other"
            placeholder="Другое"
          />
      </div>
    </div>

    <!-- Текст обращения -->
    <p class="branch">Описание проблемы<span>*</span></p>
    <textarea
      form="mainForm"
      @input="inputChange()"
      ref="msgTEXT"
      v-model="message"
      placeholder="хочу расторгнуть договор"
    ></textarea>

    <!-- Файлы-->
    <div class="files__wrapper">
      <p class="branch">Загрузка документов</p>
      <p class="notice">
        Приложите пожалуйста, полноэкранный скриншот. <br />
        Это поможет быстрее решить проблему.
      </p>
      <input
      form="mainForm"
      enctype = 'multipart/form-data'
      type="file" name="" id="" />
    </div>

    <button 
    @click="submitForm()"
    disabled ref="sendBtn">Отправить</button>
  </section>
</template>

<script>
export default {
  name: "Form",

  beforeCreate: function () {
    let getCities = new XMLHttpRequest();

    getCities.onload = () => {
      let cities = JSON.parse(getCities.response);
      for (let x = 0; x < cities.length; x++) {
        this.options.push({
          value: cities[x].id,
          text: cities[x].title,
          id: cities[x].id,
        });
      }
    };
    getCities.open(
      "get",
      "https://60254fac36244d001797bfe8.mockapi.io/api/v1/city",
      true
    );
    getCities.send();
  },

  data() {
    return {
      selected: "Выберите город",
      options: [],
      checked: false,
      picked: "",
      message: "",
      radiomassage : "",
    };
  },

  methods: {
    cityBlock() {
      if (this.checked === false) {
        this.$refs.citySelect.setAttribute("disabled", undefined);
      } else {
        this.$refs.citySelect.removeAttribute("disabled", undefined);
      }
    },

    colorChange() {
      if (this.selected != "Выберите город") {
        this.$refs.citySelect.classList.remove("select_grey");
      }
    },

    

    inputChange() {
      if (
        this.message !== "" &&
        (this.selected !== "Выберите город" || this.checked === true) &&
        ( this.picked !== '' || this.radiomassage !== '') 
      ) {
        this.$refs.sendBtn.removeAttribute("disabled", undefined);
      } else {
        this.$refs.sendBtn.setAttribute("disabled", undefined);
      }
    },

    radioChange() {
      if (this.radiomassage !== "") {
        this.picked = ''
      }
    },

    otherClear() {
      if( this.picked != ''){
        this.radiomassage = ''
      }
    },

    submitForm(){
      let oReq = new XMLHttpRequest();

      oReq.onload = ()=> {
        let responseStatus = (JSON.parse(oReq.response))
        if( responseStatus.success === false){
          alert('Ошибка отправки заявки.')
        }
        else {
          this.$router.replace('/SendSucces')
        }
        console.log(responseStatus);
      }

      oReq.open("post", "https://60254fac36244d001797bfe8.mockapi.io/api/v1/send-form", true);
      oReq.send(); 
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

// Инпуты
input {
  outline: none;
}
input[type="text"] {
  height: 30px;
  margin-top: 10px;
  padding-left: 5px;
}

textarea {
  outline: none;
  padding: 5px;
  font-size: 12px;
  width: 100%;
  height: 150px;
}

button {
  padding: 5px 10px;
  outline: none;
  border: none;
  margin-top: 30px;
  background-color: #ff9765;
  color: white;
}
button:hover {
  opacity: 0.8;
}

button:disabled {
  background-color: #e2e2e2;
}

// Чекбоксинпут замены стандартного стиля
input[type="text"]:hover {
  border: 1px solid #000000;
}
input[type="text"]:focus {
  border: 1px solid #000000;
}
input[type="checkbox"] {
  position: relative;
}
input[type="checkbox"]::before {
  content: "";
  top: 0;
  left: -1px;
  top: -1px;
  position: absolute;
  width: 20px;
  height: 20px;
  background-color: white;
  border: 1px solid #c4c4c4;
}
input[type="checkbox"]:hover::before {
  border: 1px solid #000000;
}
input[type="checkbox"]:checked::after {
  content: "";
  position: absolute;
  top: 0px;
  left: 0px;
  width: 18px;
  height: 18px;
  background: url("../assets/regForm_li.svg") no-repeat;
  background-position: 50% 50%;
  background-size: 70%;
  background-color: white;
  visibility: visible;
}
input[type="checkbox"]:checked::before {
  border: 1px solid #000000;
  position: absolute;
}
// /чекбос^

// Инпут радиокнопка заменя стандартного стиля
input[type="radio"] {
  position: relative;
}
input[type="radio"]::before {
  content: "";
  position: absolute;
  top: 50%;
  left: 50%;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  transform: translate(-50%, -50%);
  background-color: white;
  border: 1px solid #c4c4c4;
}
input[type="radio"]:checked::after {
  content: "";
  position: absolute;
  top: 50%;
  left: 50%;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background-color: black;
  transform: translate(-50%, -50%);
  visibility: visible;
}
input[type="radio"]:checked::before {
  border: 1px solid #000000;
}
input[type="radio"]:hover::before {
  border: 1px solid #000000;
}
// /радиокнопка^

section {
  padding: 30px;
}

.radio__wrapper {
  margin: 30px 0;
  label {
    margin-left: 10px;
  }
  input {
    margin-top: 5px;
  }
}

.other__wrapper {
  display: flex;
  align-items: center;
  height: 40px;
}

.branch {
  margin: 10px 0;
  span {
    text-transform: uppercase;
    color: red;
  }
}

.cityCheck {
  margin-top: 20px;
}

select {
  height: 30px;
  outline: none;
  background-color: rgb(240, 240, 240);
  .choose_opt {
    color: grey;
  }
  .city_opt {
    color: black;
  }
}

.select_grey {
  color: grey;
}
.cityCheck {
  margin-top: 15px;
  label {
    margin-left: 10px;
  }
}

.files__wrapper {
  margin-top: 30px;
  .notice {
    font-size: 12px;
  }
  input {
    margin-top: 20px;
  }
}
</style>
