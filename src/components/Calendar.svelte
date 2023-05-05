
<script lang="ts">
  import { onMount } from "svelte";
  import type { Day } from "../models/DayType";
  import type { HistoryGameType } from "../models/HistoryGameType";
  import { nextArrow, prevArrow } from "../assets/img";

  const  dayNames = ["пн", "вт", "ср", "чт", "пт", "сб", "вс"];
	const monthNames = ["Январь", "Февраль", "Март", "Апрель", "Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь"];


	let now: Date = new Date();
	let year:number = now.getFullYear();	
	let month:number = now.getMonth();
	let days: Day[] = [];

	$: month,year, initMonth();

	function initMonth(): void {
		days = [];

		const firstDay: number = new Date(year, month, 0).getDay();
		const daysInThisMonth: number = new Date(year, month+1, 0).getDate();
    const daysInLastMonth: number = new Date(year, month, 0).getDate();
		const prevMonth: number = month == 0 ? 11 : month-1;

		for (let i: number = daysInLastMonth-firstDay; i<daysInLastMonth; i++) {
			let d: string = new Date(prevMonth==11 ? year-1 : year, prevMonth,i+1).toLocaleDateString("en-GB");
			days.push({name:''+(i+1),enabled:false,date:d,});
		}
		for (let i : number = 0; i<daysInThisMonth; i++) {
			let d: string = new Date(year , month, i+1).toLocaleDateString("en-GB");
			days.push({name:''+(i+1),enabled:true,date:d,});
		}
		for (let i: number = 0; days.length%7; i++) {
			let d: string = new Date((month == 11 ? year+1 : year),(month+1)%12,i+1).toLocaleDateString("en-GB");
			days.push({name:''+(i+1),enabled:false,date:d,});
		}
	}

	function next(): void {
		month++;
		if (month == 12) {
 			year++;
			month=0;
		}
	}
	function prev(): void {
		if (month==0) {
			month=11;
			year--;
		} else {
			month--;
		}
	}

  const historyGames: HistoryGameType[] = [
    {
      date: "29/04/2023",
      title: "DFGDFGGD",
      time: "19:00"
    },
    {
      date: "28/04/2023",
      title: "Игра: DFGDFGGD",
      time: "16:00"
    },
    {
      date: "21/05/2023",
      title: "Игра: DFGDFGGD",
      time: "11:00",
    }
  ]

  let openDayHistory: undefined | string;

  function isGames(day: string): boolean {
    for (let plan of historyGames) {
      if (plan.date === day) {
        return true;
      }
    }
  }

  onMount(() => initMonth)
</script>


<div class="calendar">
  <div class="calendar__title">
    <h2>{monthNames[month]} {year}</h2>
    <div class="nav">
      <button on:click={()=>prev()}>
        <img src={prevArrow} alt="">
      </button>
      <button on:click={()=>next()}>
        <img src={nextArrow} alt="">
      </button>
    </div>
  </div>
  <div class="calendar__days">
    {#each dayNames as name}
      <span class="day-name">{name}</span>
    {/each}
    {#each days as day}
      <spa
        class="day"
        class:disabled={!day.enabled}
        class:today={now.toLocaleDateString('en-GB') === day.date}
        class:is-games={isGames(day.date)}
        class:active={openDayHistory === day.date}
        on:click={() => openDayHistory === day.date ? openDayHistory = undefined : openDayHistory = day.date}
      >
        {day.name}
      </spa>
      {/each}
    </div>
</div>




<style lang="scss">
.calendar {
  &__title {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding-top: 12px;
    padding-bottom: 21px;

    .nav {
      display: flex;
      gap: 16px;
      button {
        border: none;
        background: none;
        cursor: pointer;
        padding: 0;
      }
    }

    h2 {
      font-weight: 500;
      font-size: 16px;
      line-height: 20px;
      color: #fff;
    }
  }

  &__days { 
    display: grid;
    grid-template-columns: repeat(7, 24px);
    grid-template-rows: 24px;
    grid-auto-rows: 24px;
    gap: 17px 19px;
    padding: 0 16px 0;
    overflow: auto;

    .day {
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: 400;
      font-size: 14px;
      line-height: 17px;
      color: #fff;
      cursor: pointer;
      &:nth-child(7n-1),
      &:nth-child(7n){
        color: #D4D7E0;
      }
      &.disabled {
        color: #78778A;
      }
      &.active {
        background-color: #2DD9E7;
        border-radius: 50%;
      }
      &.is-games {
        position: relative;
        &::after {
          content: "";
          width: 5px;
          height: 5px;
          position: absolute;
          background: linear-gradient(334deg, #CF691E 15%, #FFC85C 86%);
          border-radius: 50%;
          right: 0px;
          top: 0px;
        }
      }
    }
    .today {
      background: #78778A;
      border-radius: 33px;
    }

    .day-name {
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: 400;
      font-size: 15px;
      line-height: 18px;
      color: #78778A;
      text-align: center;
    }
  }
}
</style>

