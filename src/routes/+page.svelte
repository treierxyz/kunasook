<script lang="ts">
  enum Unit {
    Kasutajatugi = 0,
    Arendus = 1,
    StratKomKe = 2,
    KIOKe = 3,
    StTaKo = 3,
  }

  enum Meal {
    Breakfast = 0,
    Lunch = 1,
    Dinner = 2,
  }

  const hoursToMills = (x: number) => x * 3600 * 1000;

  const breakfastBase = 7;
  const lunchBase = 12 + 35 / 60;
  const dinnerBase = 18;

  function getUnitOrderFromWeek(week: number, unit: Unit) {
    return ((-week % 4) + 4 + unit) % 4;
  }

  function getYearWeek(date: Date = new Date()) {
    date.setHours(0, 0, 0, 0);
    date.setDate(date.getDate() + 3 - ((date.getDay() + 6) % 7));
    const week1 = new Date(date.getFullYear(), 0, 4);
    return (
      1 +
      Math.round(
        ((date.getTime() - week1.getTime()) / 86400000 -
          3 +
          ((week1.getDay() + 6) % 7)) /
          7
      )
    );
  }

  function getOrdinalDay(date: Date) {
    const startDate = new Date("2025-09-08T00:00:00");
    return Math.floor(
      (date.getTime() - startDate.getTime()) / (1000 * 3600 * 24)
    );
  }

  function getMealTime(meal: Meal, unit: Unit, date: Date): Date {
    const pureDate = structuredClone(date);
    pureDate.setHours(0, 0, 0, 0);
    const ordinalDay = getOrdinalDay(date);
    const ordinalWeek = Math.floor(ordinalDay / 7);
    const offset = getUnitOrderFromWeek(ordinalWeek, unit) * (10 / 60);

    let mealTime: Date;

    switch (meal) {
      case Meal.Breakfast:
        mealTime = new Date(
          pureDate.getTime() + hoursToMills(breakfastBase + offset)
        );
        break;
      case Meal.Lunch:
        mealTime = new Date(
          pureDate.getTime() + hoursToMills(lunchBase + offset)
        );
        break;
      case Meal.Dinner:
        mealTime = new Date(
          pureDate.getTime() + hoursToMills(dinnerBase + offset)
        );
        break;
    }
    return mealTime;
  }

  const testdate = new Date("2025-10-14T12:34:56");

  console.log(getMealTime(Meal.Breakfast, Unit.Arendus, testdate));
</script>

<section class="flex flex-col items-center justify-center h-full gap-3">
  <section class="text-lg sm:text-xl text-center mx-6">
    <label for="">On {getYearWeek()}. nädal, ehk </label>
    <select name="" id="" class="border-1 rounded-sm px-1 border-gray-500">
      <option value="">arendusel</option>
      <option value="">kasutajatoel</option>
      <option value="">KIOKe-l</option>
      <option value="">StTaKo-l</option>
      <option value="">StratKomKe-l</option>
    </select>
    <label for="">on õhtusöök:</label>
  </section>
  <h1 class="text-8xl sm:text-9xl font-bold pointer-events-none select-none">
    18:15
  </h1>
</section>

<style lang="scss">
</style>
