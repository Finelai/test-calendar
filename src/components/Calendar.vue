<template>
  <div class="calendar">
    <div class="calendar__grid flex-grid">
      <div class="flex-grid__item" v-for="(item, index) in 35" :key="index">
        {{ index+1 }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'calendar',
  data () {
    return {
      curYear: '',
      curMonth: '',
      curDay: '',
      header: 'Календарь на',
    }
  },
  created() {
    this.getCurDay();
    this.$emit('header', `${this.header} ${this.curDay} - ${this.curMonth} - ${this.curYear}`);
  },
  methods: {
    getCurDay() {
      // получаем текущие данные и записываем их
      const curDate = new Date();
      this.curYear = curDate.getFullYear();
      this.curMonth = curDate.getMonth();
      this.curDay = curDate.getDate();
      const curWeekDay = curDate.toLocaleString('ru', {weekday: 'short'});
      const weeksDaysArray = [ 'пнд', 'вт', 'ср', 'чтв', 'пн', 'сб', 'вс' ];
      const daysBefore = weeksDaysArray.indexOf(curWeekDay);
      console.log(curWeekDay, weeksDaysArray.indexOf(curWeekDay));
      if (daysBefore === 0) {
        // текущая дата понедельник
      } else if (daysBefore = this.curDay) {
        // последнее число предыдущего месяца
      } else if (daysBefore < this.curDay) {
        // this.curDay - daysBefore и получаем число дней текущего месяца
        // выучисляем дни в текущем месяце и вычитаем из них полученное число
        // результат прибавляем к след месяцу
      } else if (daysBefore > this.curDay) {
        // daysBefore - this.curDay и получаем число дней предыдущего месяца
        // вычитаем из числа дней полученное значение
        // 
      }
      // сравниваем количество дней до понедельника и текущее число
      // если дней меньше, то вычитаем
      // если дней больше, то вычитаем из дней до пнд текущее число
      // - остаток вычитаем из предыдущего месяца предварительно вычислив его количество дней
      // берем число понедельника и в цикле добавляем в массив 35 дней начиная с полученного числа, указав до запятой день недели
      // при выводе первые 6 индексов выводим с запятой, а потом только после запятой
    }
  },
}
</script>

<style src="../styles/components/calendar.scss" lang="scss" scoped></style>
