<script setup>
import { ref, computed } from 'vue'

const apartmentPrice = ref(4000000)
const downPayment = ref(1000000)
const creditTerm = ref(10)
const loanRate = ref(11.7)

const apartmentPriceText = computed(() => Number(apartmentPrice.value).toLocaleString('ru-RU'))
const downPaymentText = computed(() => Number(downPayment.value).toLocaleString('ru-RU'))

const creditTermMonth = computed(() => creditTerm.value * 12)
const loanRateMonth = computed(() => loanRate.value / 1200)
const amountCredit = computed(() => apartmentPrice.value - downPayment.value)
const monthlyPayment = computed(() =>
  creditTerm.value && loanRate.value
    ? Math.ceil(
        (loanRateMonth.value + loanRateMonth.value / (Math.pow(1 + loanRateMonth.value, creditTermMonth.value) - 1)) *
          amountCredit.value,
      )
    : 0,
)
const totalPayout = computed(() => Math.ceil(monthlyPayment.value * 12 * creditTerm.value))
const overpayment = computed(() => (creditTerm.value && loanRate.value ? totalPayout.value - amountCredit.value : 0))
const overpaymentPercent = computed(() =>
  amountCredit.value && loanRate.value ? Math.ceil(overpayment.value / (amountCredit.value / 100)) : 0,
)

const endPayments = computed(
  () => `${new Date().toLocaleString('default', { month: 'long' })} ${new Date().getFullYear() + creditTerm.value}`,
)

const percent = computed(() => (+apartmentPrice.value + overpayment.value) / 100)

const styleSum = computed(() => ({
  width: `${amountCredit.value / percent.value}%`,
}))
const styleOverpayment = computed(() => ({
  width: `${overpayment.value / percent.value}%`,
}))
const styleDownPayment = computed(() => ({
  width: `${+downPayment.value / percent.value}%`,
}))

function changeApartmentPrice(value) {
  apartmentPrice.value = Number(value.replace(/\D/g, ''))
}
function changeDownPayment(value) {
  downPayment.value = Number(value.replace(/\D/g, ''))
}
</script>

<template>
  <div class="mortgage">
    <h1 class="mortgage-title">Ипотечный калькулятор</h1>
    <p class="mortgage-text">
      Рассчитайте ипотеку калькулятором онлайн - узнайте платежи и график погашения кредита в банках. Чтобы выбрать
      выгодный вариант и рассчитать ипотечный кредит калькулятором, введите параметры и посмотрите переплаты в выбранных
      банках России, выберите наиболее подходящий кредит и оставьте заявку.
    </p>

    <main class="mortgage-body">
      <section class="mortgage-settings">
        <article class="mortgage-settings-item">
          <span class="mortgage-settings-item__title">Стоимость недвижимости, ₽</span>
          <input
            class="mortgage-settings-item__input"
            :value="apartmentPriceText"
            type="text"
            @input="changeApartmentPrice($event.target.value)"
          />
          <div class="mortgage-settings-item-range">
            <input
              type="range"
              v-model="apartmentPrice"
              class="mortgage-settings-item-range__input"
              min="1000000"
              max="20000000"
            />
          </div>
        </article>

        <article class="mortgage-settings-item">
          <span class="mortgage-settings-item__title">Первоначальный взнос, ₽</span>
          <input
            type="text"
            :value="downPaymentText"
            class="mortgage-settings-item__input"
            @input="changeDownPayment($event.target.value)"
          />
          <div class="mortgage-settings-item-range">
            <input
              type="range"
              v-model="downPayment"
              class="mortgage-settings-item-range__input"
              min="0"
              :max="apartmentPrice"
            />
          </div>
        </article>

        <article class="mortgage-settings__amount">
          Сумма кредита:
          <b>{{ amountCredit.toLocaleString('ru') }}</b>
          ₽
        </article>

        <article class="mortgage-settings__conditions">
          <div class="mortgage-settings-item mortgage-settings-item_left">
            <span class="mortgage-settings-item__title">Срок кредита, лет</span>
            <input type="number" v-model="creditTerm" class="mortgage-settings-item__input" min="0" max="30" />
          </div>
          <div class="mortgage-settings-item mortgage-settings-item_right">
            <span class="mortgage-settings-item__title">Ставка, %</span>
            <input type="number" v-model="loanRate" class="mortgage-settings-item__input" min="0" max="100" />
          </div>
        </article>

        <!--ссылка от заказчка-->
        <a class="mortgage-settings__button" href="zakazat-zvonok" id="zvonok-form" data-fancybox-type="iframe">
          Получить консультацию
        </a>
      </section>

      <section class="mortgage-result">
        <div class="mortgage-result-text">
          <div class="mortgage-result-text-item">
            <span class="mortgage-result-text-item__circle mortgage-result-diagram_color_green"></span>
            <span class="mortgage-result-text-item__title">Первоначальный взнос</span>
          </div>
          <div class="mortgage-result-text-item">
            <span class="mortgage-result-text-item__circle mortgage-result-diagram_color_emerald"></span>
            <span class="mortgage-result-text-item__title">Кредит</span>
          </div>
          <div class="mortgage-result-text-item">
            <span class="mortgage-result-text-item__circle mortgage-result-diagram_color_yellow"></span>
            <span class="mortgage-result-text-item__title">Переплата</span>
          </div>
        </div>
        <div class="mortgage-result-diagram">
          <div
            class="mortgage-result-diagram__line mortgage-result-diagram_color_green"
            :style="styleDownPayment"
          ></div>
          <div class="mortgage-result-diagram__line mortgage-result-diagram_color_emerald" :style="styleSum"></div>
          <div
            class="mortgage-result-diagram__line mortgage-result-diagram_color_yellow"
            :style="styleOverpayment"
          ></div>
        </div>

        <article class="mortgage-result-condition">
          <span class="mortgage-result-condition__text">Платёж в месяц</span>
          <span class="mortgage-result-condition__value mortgage-result-condition__value_rubles">
            {{ monthlyPayment.toLocaleString('ru') }}
          </span>
        </article>
        <article class="mortgage-result-condition">
          <span class="mortgage-result-condition__text">Сумма кредита</span>
          <span class="mortgage-result-condition__value mortgage-result-condition__value_rubles">
            {{ amountCredit.toLocaleString('ru') }}
          </span>
        </article>
        <article class="mortgage-result-condition first-credit-summ">
          <span class="mortgage-result-condition__text">Первоначальный взнос</span>
          <span class="mortgage-result-condition__value mortgage-result-condition__value_rubles">
            {{ downPayment.toLocaleString('ru') }}
          </span>
        </article>
        <article class="mortgage-result-condition unlimit-credit-summ">
          <span class="mortgage-result-condition__text">Переплата по кредиту</span>
          <span class="mortgage-result-condition__value mortgage-result-condition__value_rubles">
            {{ overpayment.toLocaleString('ru') }}
          </span>
        </article>
        <article class="mortgage-result-condition all-credit-summ">
          <span class="mortgage-result-condition__text">Общая сумма выплат</span>
          <span class="mortgage-result-condition__value mortgage-result-condition__value_rubles">
            {{ totalPayout.toLocaleString('ru') }}
          </span>
        </article>
        <article class="mortgage-result-condition percent-credit-summ">
          <span class="mortgage-result-condition__text">Процент переплаты</span>
          <span class="mortgage-result-condition__value mortgage-result-condition__value_percent">
            {{ overpaymentPercent }}
          </span>
        </article>
        <article class="mortgage-result-condition date-credit-summ">
          <span class="mortgage-result-condition__text">Окончание выплат</span>
          <span class="mortgage-result-condition__value mortgage-result-condition__value_year">
            {{ endPayments }}
          </span>
        </article>
      </section>
    </main>
  </div>
</template>

<style lang="scss">
.mortgage {
  font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
  line-height: 1.5;
  font-weight: 400;

  font-synthesis: none;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  -webkit-text-size-adjust: 100%;
  font-size: 13px;
  color: #282828;
  margin: 30px 50px;
}

.mortgage-title {
  font-size: 35px;
  font-weight: 500;
}
.mortgage-text {
  margin-block-start: 1em;
  margin-block-end: 1em;
  margin-inline-start: 0;
  margin-inline-end: 0;
  font-size: 14px;
}

.mortgage-body {
  display: flex;
}

.mortgage-settings,
.mortgage-result {
  border-radius: 15px;
  background-color: #f0f3f5;
  padding: 15px;
}

.mortgage-settings {
  width: 33%;
  padding-top: 30px;
  margin-right: 10px;
}
.mortgage-result {
  width: 77%;
  padding-top: 30px;
  margin-left: 10px;
}

.mortgage-settings-item {
  position: relative;
  margin-bottom: 30px;
}

.mortgage-settings-item__title {
  position: absolute;
  top: -7px;
  font-size: 12px;
  left: 5px;
  background-color: #f0f3f5;
  padding: 0 6px;
}
.mortgage-settings-item__input {
  width: 100%;
  box-sizing: border-box;
  height: 40px;
  font-size: 14px;
  font-weight: 500;
  border: 1px solid #c1d6e4;
  background-color: #f0f3f5;
  padding-left: 10px;
  outline: none;
}

.mortgage-settings-item_left {
  width: 65%;
  margin-right: 10px;
}
.mortgage-settings-item_right {
  width: 35%;
}

.mortgage-settings__amount {
  margin-bottom: 20px;
}

.mortgage-settings__conditions {
  display: flex;
}

.mortgage-settings-item-range {
  position: absolute;
  bottom: -6px;
  left: 0;
  width: 100%;
}
.mortgage-settings-item-range__input {
  position: relative;
  width: 100%;
  left: -1px;
  height: 4px;
  background: #c1d6e4;
  -webkit-appearance: none;
  outline: none;
  border-radius: 1px;
}

.mortgage-settings__button {
  display: flex;
  color: #fff;
  background-image: linear-gradient(90deg, #f6bf5b, #eb9033);
  padding: 10px 30px;
  text-align: center;
  font-weight: 700;
  flex-wrap: wrap;
  min-width: 160px;
  justify-content: center;
  align-items: center;
  text-shadow: 1px 1px 1px rgba(150, 150, 150, 0.67);
  box-sizing: border-box;
  border-radius: 5px;
}

.mortgage-result-text {
  display: flex;
  flex-flow: row wrap;
  margin-bottom: 10px;
}
.mortgage-result-diagram {
  width: 100%;
  border-radius: 4px;
  overflow: hidden;
  background: #eceff1;
  height: 8px;
  display: flex;
  flex-direction: row;
  margin-bottom: 30px;
}
.mortgage-result-diagram__line {
  transition: width 1s;
  height: 8px;
}

.mortgage-result-diagram_color_green {
  background-color: rgb(10, 96, 4);
}
.mortgage-result-diagram_color_emerald {
  background-color: rgb(46, 204, 113);
}
.mortgage-result-diagram_color_yellow {
  background-color: rgb(255, 201, 46);
}

.mortgage-result-text-item {
  flex: 0 1 auto;
  display: flex;
  align-items: baseline;
  margin-right: 16px;
}
.mortgage-result-text-item__circle {
  width: 8px;
  height: 8px;
  display: inline-block;
  border-radius: 50%;
  margin-right: 4px;
}
.mortgage-result-text-item__title {
  font-size: 12px;
  line-height: 16px;
}

.mortgage-result-condition {
  display: flex;
  justify-content: space-between;
  border-top: 1px solid #ccc;
  padding: 10px 0;
  align-items: center;
}
.mortgage-result-condition__text {
  font-size: 14px;
  opacity: 0.7;
}
.mortgage-result-condition__value {
  font-size: 16px;
  font-weight: 700;
  display: inline;
}

.mortgage-result-condition__value:after {
  display: inline;
  font-weight: 400;
  opacity: 0.9;
  font-size: 14px;
}

.mortgage-result-condition__value_rubles:after {
  content: ' ₽';
}
.mortgage-result-condition__value_year:after {
  content: ' г.';
}
.mortgage-result-condition__value_percent:after {
  content: ' %';
}

@media (max-width: 940px) {
  .mortgage-body {
    display: block;
  }

  .mortgage-settings,
  .mortgage-result {
    width: 100%;
    margin: 0;
  }
  .mortgage-result {
    margin-top: 30px;
  }
}
</style>
