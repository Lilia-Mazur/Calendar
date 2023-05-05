<script>
  import Calendar from "./Calendar.svelte";

</script>
<div class="events">
  <p class="events__title">
    События и мероприятия
  </p>

  <Calendar />
</div>

<style lang="scss">
  .events {
    width: 100%;
    max-width: 348px;
    min-height: 373px;
    display: flex;
    justify-content: start;
    flex-direction: column;
    padding: 16px;
    padding-bottom: 0;
    background: linear-gradient(144deg, rgba(78, 77, 96, 0.6) 65%, rgba(174, 120, 225, 0.6) 181%);
    border-radius: 18px;

    &__title {
      font-weight: 500;
      font-size: 16px;
      line-height: 20px;
      color: #fff;
    }
  }
</style>