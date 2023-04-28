
<script>
	// import {createEventDispatcher, onMount} from 'svelte';
	// let dispatch = createEventDispatcher();

  const  dayNames = ["пн", "вт", "ср", "чт", "пт", "сб", "вс"];
	const monthNames = ["Январь", "Февраль", "Март", "Апрель", "Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь"];

	let headers = [];
	let now = new Date();
	let year = now.getFullYear();	
	let month = now.getMonth();

	let days = [];


	$: month,year,initContent();

	function initContent() {
		headers = dayNames;
		initMonth();
	}

	function initMonth() {
		days = [];

		const firstDay = new Date(year, month, 0).getDay();
		const daysInThisMonth = new Date(year, month+1, 0).getDate();
    const daysInLastMonth = new Date(year, month, 0).getDate();
		const prevMonth = month == 0 ? 11 : month-1;
    console.log(daysInLastMonth)

		for (let i = daysInLastMonth-firstDay; i<daysInLastMonth; i++) {
			let d = new Date(prevMonth==11 ? year-1 : year, prevMonth,i+1);
			days.push({name:''+(i+1),enabled:false,date:d,});
		}
		for (let i=0; i<daysInThisMonth; i++) {
			let d = new Date(year , month, i+1);
			days.push({name:''+(i+1),enabled:true,date:d,});
		}
		for (let i=0; days.length%7; i++) {
			let d = new Date((month == 11 ? year+1 : year),(month+1)%12,i+1);
			days.push({name:''+(i+1),enabled:false,date:d,});
		}
	}

  console.log(days);


	function next() {
		month++;
		if (month == 12) {
 			year++;
			month=0;
		}
	}
	function prev() {
		if (month==0) {
			month=11;
			year--;
		} else {
			month--;
		}
	}
  function today(day) {
    return day.getDate() === now.getDate() && day.getMonth() === now.getMonth() &&  day.getFullYear() === now.getFullYear();
  }

</script>


<div class="calendar">
  <div class="calendar__header">
    <h1>
      {monthNames[month]} {year}
    </h1>
    <div class="nav">
      <button on:click={()=>prev()}>&and;</button>
      <button on:click={()=>next()}>&or;</button>
    </div>

	</div>
  <div class="calendar__container">
    {#each headers as header}
    <span class="day-name">{header}</span>
    {/each}
  
    {#each days as day}
        <span 
          class="day"
          class:day-disabled={!day.enabled}
          class:today={today(day.date)}
        >{day.name}</span>
    {/each}
  </div>
</div>


<style lang="scss">
.calendar {
  width: max-content;
  padding: 14px 24px 17px;
  margin: auto;

  border-radius: 10px;
  background: #1E1D2B;
  border-radius: 18px;
&__header {
  display: flex;
  justify-content: space-between;
  align-items: center;

  h1 {
    margin: 0;
    font-size: 18px;
    color: #fff;
  }

  button {
    background: transparent;
    border: 1px ;
    padding: 6;
    color: #fff;
    cursor: pointer;

  }
}

  &__container { 
    display: grid;
    width: 100%;
    grid-template-columns: repeat(7, 24px);
    grid-template-rows: 24px;
    grid-auto-rows: 24px;
    gap: 8px;
    overflow: auto;

    .day {
      display: flex;
      align-items: center;
      justify-content: center;
      letter-spacing: 1px;
      font-size: 14px;
      box-sizing: border-box;
      color: #98a0a6;
      position: relative;
      z-index: 1;
    }
    .today {
      background: #78778A;
      border-radius: 33px;
    }

    .day-name {
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 12px;
      text-transform: uppercase;
      color: #78778A;;
      text-align: center;
      font-weight: 500;
    }
    .day-disabled {
      color: #78778A;
      cursor: not-allowed;
    }
  }
}
</style>

