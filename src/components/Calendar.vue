<template>
  <div class="calendar">
    <div class="calendar__header-wrap">
      <div class="calendar__header">
        <h1>{{ `${header} ${curYear} год` }}</h1>
        <button @click="addEvent">Добавить</button>
        <button @click="refreshCalendar">Обновить</button>
        <div class="calendar__search">
          <i class="search-icon"></i>
          <input type="text" placeholder="Событие, дата или участник">
        </div>
      </div>
    </div>
    <div class="calendar__grid flex-grid">
      <div v-for="(item, index) in calendar" :key="index" :class="item.class" @click="select(index)">
        <p>
          <span> <span v-if="index < 7">{{ `${item.name},` }}</span> {{ item.day }}</span>
        </p>
        <div v-if="item.eventTitle !== ''">
          <h3>{{ item.eventTitle }}</h3>
          <p>{{ item.eventDesc }}</p>
        </div>
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
      selected: null,
    }
  },
  created() {
    this.getCurDay();
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
            class: (`${this.curYear}${this.curMonth}${this.curDay}` == `${curYearToWrite}${curMonthToWrite}${curDayToWrite}`) ? 'flex-grid__item flex-grid__item--current' : 'flex-grid__item flex-grid__item--empty',
            eventTitle: '',
            eventDesc: '',
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
    select(index) {
      if (this.selected !== null) {
        this.calendar[this.selected].class = 'flex-grid__item flex-grid__item--empty';
      }
      this.calendar[index].class = 'flex-grid__item flex-grid__item--select';
      this.selected = index;
    },
    addEvent() {
      if (this.selected !== null) {
        let title = prompt("Введите заголовок события", '');
        if (title !== '' && title !== null) {
          let desc = prompt("Введите описание события", '');
          if (desc !== '' && desc !== null) {
            this.calendar[this.selected].eventTitle = title;
            this.calendar[this.selected].eventDesc = desc;
            this.selected = null;
          }
        }
      } else {
        alert('Сначала выберите дату события нажатием на ячейку');
      }
    },
    refreshCalendar() {
      this.calendar = [];
      this.getCurDay();
    },
  },
}
</script>

<style src="../styles/components/calendar.scss" lang="scss" scoped></style>
