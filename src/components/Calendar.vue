<template>
  <div class="calendar">
    <div class="calendar__grid flex-grid">
      <div class="flex-grid__item" v-for="(item, index) in calendar" :key="index">
        <p v-if="index < 7">{{ `${item.name}, ${item.day}` }}</p>
        <p v-else>{{ item.day }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'calendar',
  data () {
    return {
      header: 'Календарь на',
      curYear: '',
      curMonth: '',
      curDay: '',
      calendar: [],
    }
  },
  created() {
    this.getCurDay();
    this.$emit('header', `${this.header} ${this.curYear} год`);
  },
  methods: {
    getLastDayOfMonth(year, month) {
      const date = new Date(year, month + 1, 0);
      return date.getDate();
    },
    getWeekDay(year, month, day) {
      const date = new Date(year, month, day);
      return date.toLocaleString('ru', {weekday: 'long'});
    },
    getCurDay() {
      // получаем текущие данные и записываем их
      const curDate = new Date();
      this.curYear = curDate.getFullYear();
      this.curMonth = curDate.getMonth();
      this.curDay = curDate.getDate();
      const curWeekDay = curDate.toLocaleString('ru', {weekday: 'short'});
      const weeksDaysArray = [ 'пн', 'вт', 'ср', 'чт', 'пт', 'сб', 'вс' ];
      const daysBefore = weeksDaysArray.indexOf(curWeekDay);

      let curYearToWrite = this.curYear;
      let curMonthToWrite = this.curMonth;
      let curDayToWrite = this.curDay;
      let maxDays = this.getLastDayOfMonth(curYearToWrite, curMonthToWrite);

      if (daysBefore > 0) {
        // текущая дата не понедельник
        let diff = curDayToWrite - daysBefore;
        if (diff < 0) {
          // переход на прошлый месяц
          if (curMonthToWrite === 0) {
            curYearToWrite--;
            curMonthToWrite = 11;
          } else {
            curMonthToWrite--;
          }
          curDayToWrite = maxDays - Math.abs(diff);
        } else {
          // без перехода на прошлый месяц
          curDayToWrite = diff;
        }
        maxDays = this.getLastDayOfMonth(curYearToWrite, curMonthToWrite);
      }

      for (let n = 0; n < 36; n++) {
        if (curDayToWrite <= maxDays) {
          this.calendar.push({
            day: curDayToWrite,
            name: this.getWeekDay(curYearToWrite, curMonthToWrite, curDayToWrite),
          });
        } else {
          curMonthToWrite++;
          if (curMonthToWrite > 11) {
            curMonthToWrite = 0;
            curYearToWrite++;
            n--;
          }
          maxDays = this.getLastDayOfMonth(curYearToWrite, curMonthToWrite);
          curDayToWrite = 0;
        }
        curDayToWrite++;
      }
    },
  },
}
</script>

<style src="../styles/components/calendar.scss" lang="scss" scoped></style>
