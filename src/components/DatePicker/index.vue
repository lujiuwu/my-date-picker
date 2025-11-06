<template>
  <div class="my-date-picker">
    <div class="title">
      <img src="../../assets/dla.svg" height="20" width="20">
      <img src="../../assets/la.svg" height="20" width="20">
      <span class="main__title" style="margin-bottom: 5px;">
        {{ year }}年{{ mouth }}月
      </span>
      <img src="../../assets/ra.svg" height="20" width="20">
      <img src="../../assets/dra.svg" height="20" width="20">
    </div>
    <div class="calender">
      <div class="calender__title__weeks">
        <div class="item" v-for="(item, index) in weeks" :key="index">{{ item }}</div>
      </div>
      <div class="calender__body">
        <div class="item" v-for="(item, index) in calender" :key="index">
          <div
           class='item__day'
           :class="{
             'prev__month__content': index === 0 && idx < firstDayOfMouth,
             'next__month__content': index === 5 && idx > item.indexOf(daysInMouth)+1,
             'item__day-active': day.month === activeDay.month && day.year === activeDay.year && day.day === activeDay.day
           }"
           v-for="(day, idx) in item" :key="idx"
           @click="handleDayClick(day)"
          >
           {{ day.day }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
  import { ref } from 'vue';

  interface Item {
    month: number
    year: number
    day: number
  }

  const weeks = ref<string[]>(['日', '一', '二', '三', '四', '五', '六'])

  const date = new Date()

  const mouth = date.getMonth() + 1
  const year = date.getFullYear()

  const activeDay = ref<Item>({month: date.getMonth() + 1, year: date.getFullYear(), day: date.getDate()})

  // 当前月份天数
  const daysInMouth = new Date(year, mouth, 0).getDate()
  // 当前月份1号是星期几
  const firstDayOfMouth = new Date(year, mouth - 1, 1).getDay()
  
  function formCalender() {
    // 日历结构 -- 7(星期数) * (周数)
    const calender = new Array(6).fill(null).map(() => new Array<Item>(7).fill({month: 0, year: 2025, day: 0}))
    // 当前周数
    const week = ref(0)
    // 当前星期几
    const day = ref(firstDayOfMouth)
  
    let weekData = []

    for(let currentDate = 1;currentDate <= daysInMouth; currentDate++) {
      if(day.value > 6) {
        day.value = 0
        calender[week.value] = weekData
        week.value++
        weekData = []
      }
      
      weekData[day.value] = {month: mouth, year: year, day: currentDate}
      day.value++
    }

    calender[week.value] = weekData
    day.value = firstDayOfMouth

    // 上个月天数
    let lastMonthDays = new Date(year, mouth - 1, 0).getDate()
    
    for(let lastDay = day.value-1;lastDay >= 0; lastDay--) {
      calender[0]![lastDay] = {month: mouth - 1, year: year, day: lastMonthDays--}
    }

    if(calender[5] && calender[5].length < 7) {
      for(let nextDay = 1;nextDay <7; nextDay++) {
        calender[5]![nextDay] = {month: mouth + 1, year: year, day: nextDay}
      }
    }

    return calender
  }

  function handleDayClick(day: Item) {
    activeDay.value = day
  }


  const calender = formCalender()

</script>
<style scope lang="scss">
.my-date-picker {
  width: 300px;
  height: 270px;
  border: 1px solid #3a3a3a;

  .title {
    display: flex;

    .main__title {
      flex: 5;
      text-align: center;
    }

    img {
      flex: 1;
      cursor: pointer;
      vertical-align: middle;
    }
  }

  .calender__title__weeks {
    display: flex;
    gap: 3px;
    border-bottom: 1px solid #3a3a3a;

    .item {
      flex: 1;
      padding: 6px 8px;
      font-weight: bold;
      text-align: center;
    }
  }

  .calender__body {
    display: grid;
    flex-direction: column;

    .item {
      display: flex;
      gap: 3px;

      .item__day {
        flex: 1;
        padding: 6px 8px;
        text-align: center;
        cursor: pointer;
      }

      .item__day-active {
        background-color: #007bff;
        color: #fff;
      }

      .prev__month__content, .next__month__content {
        color: #999;
        cursor: not-allowed;
        pointer-events: none;
      }
    }
  }
}
</style>