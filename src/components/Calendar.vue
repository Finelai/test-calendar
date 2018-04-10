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
    <div class="calendar__navigation">
      <button class="arrow arrow__left" @click="prevMonth"></button>
      <span>{{ `${monthDisplay} ${curYear}` }}</span>
      <button class="arrow arrow__right" @click="nextMonth"></button>
      <button @click="refreshCalendar">Сегодня</button>
    </div>
    <div class="calendar__grid flex-grid">
      <div v-for="(item, index) in calendar" :key="index" :class="item.class" @click="select(index)">
        <p>
          <span v-if="index < 7">{{ `${item.name},` }}</span> <span>{{ item.day }}</span>
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

      nowDate: new Date(),
      nowYear: '',
      nowMonth: '',
      nowDay: '',

      curYear: '',
      curMonth: '',
      curDay: '',

      calendar: [],
      selected: null,
    }
  },
  computed: {
    monthDisplay() {
      const monthsArray = [ 'Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь' ];
      return monthsArray[this.curMonth - 1];
    }
  },
  created() {
    // определяем и записываем сегодняшнюю дату
    this.nowYear = this.curYear = this.nowDate.getFullYear();
    this.nowMonth = this.curMonth = this.nowDate.getMonth();
    this.nowDay = this.curDay = this.nowDate.getDate();

    this.getCurCalendar();
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
    getDateAgo(date, days) {
      const dateCopy = new Date(date);
      dateCopy.setDate(date.getDate() - days);
      return [dateCopy.getFullYear(), dateCopy.getMonth(), dateCopy.getDate()];
    },
    getCurCalendar() {
      const curDate = new Date(this.curYear, this.curMonth, this.curDay);
      const curWeekDay = curDate.toLocaleString('ru', {weekday: 'short'});
      const weeksDaysArray = [ 'пн', 'вт', 'ср', 'чт', 'пт', 'сб', 'вс' ];
      const daysBefore = weeksDaysArray.indexOf(curWeekDay);

      let maxDays = this.getLastDayOfMonth(this.curYear, this.curMonth);

      if (daysBefore > 0) {
        // текущая дата не понедельник
        let diff = this.curDay - daysBefore;
        if (diff < 0) {
          // переход на прошлый месяц
          if (this.curMonth === 0) {
            this.curYear--;
            this.curMonth = 11;
          } else {
            this.curMonth--;
          }
          this.curDay = maxDays - Math.abs(diff);
        } else {
          // без перехода на прошлый месяц
          this.curDay = diff;
        }
        maxDays = this.getLastDayOfMonth(this.curYear, this.curMonth);
      }

      for (let n = 0; n < 36; n++) {
        if (this.curDay <= maxDays) {
          this.calendar.push({
            day: this.curDay,
            name: this.getWeekDay(this.curYear, this.curMonth, this.curDay),
            class: (`${this.nowYear}${this.nowMonth}${this.nowDay}` == `${this.curYear}${this.curMonth}${this.curDay}`) ? 'flex-grid__item flex-grid__item--current' : 'flex-grid__item flex-grid__item--empty',
            eventTitle: '',
            eventDesc: '',
          });
        } else {
          this.curMonth++;
          if (this.curMonth > 11) {
            this.curMonth = 0;
            this.curYear++;
            n--;
          }
          maxDays = this.getLastDayOfMonth(this.curYear, this.curMonth);
          this.curDay = 0;
        }
        this.curDay++;
      }
    },
    nextMonth() {
      this.calendar = [];
      if (this.getLastDayOfMonth(this.curYear, this.curMonth) >= (this.curDay + 1)) {
        this.curDay++;
      } else {
        this.curDay = 1;
        if ((this.curMonth + 1) > 11) {
          this.curMonth = 0;
          this.curYear++;
        } else {
          this.curMonth++;
        }
      }
      this.getCurCalendar();
    },
    prevMonth() {
      this.calendar = [];
      const date = new Date(this.curYear, this.curMonth, this.curDay);
      console.log(this.getDateAgo(date, 70));
      const prevDate = this.getDateAgo(date, 70);
      this.curYear = prevDate[0];
      this.curMonth = prevDate[1];
      this.curDay = prevDate[2];
      this.getCurCalendar();
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
      this.curYear = this.nowYear;
      this.curMonth = this.nowMonth;
      this.curDay = this.nowDay;
      this.calendar = [];
      this.getCurCalendar();
    },
  },
}
</script>

<style src="../styles/components/calendar.scss" lang="scss" scoped></style>
