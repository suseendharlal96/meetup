<script>
  import MeetupItem from "./MeetupItem.svelte";
  import Button from "../UI/Button.svelte";

  export let meetups;
  console.log(meetups);
  let isFavourite = false;
  $: actualMeetups = isFavourite
    ? meetups.filter(data => data.isFavorite === true)
    : meetups;

  function setFav(val) {
    if (val === 1) {
      isFavourite = true;
    } else {
      isFavourite = false;
    }
  }
</script>

<style>
  section {
    width: 100%;
    display: grid;
    grid-template-columns: 1fr;
    grid-gap: 1rem;
  }

  .filter {
    padding-top: 2rem;
    width: 50%;
    display: block;
  }

  @media (min-width: 768px) {
    section {
      grid-template-columns: repeat(2, 1fr);
    }
  }
</style>

<div class="filter">
  <Button
    type="button"
    color={!isFavourite ? 'success' : null}
    on:click={() => setFav(0)}>
    All
  </Button>
  <Button
    type="button"
    color={isFavourite ? 'success' : null}
    on:click={() => setFav(1)}>
    Favorites
  </Button>
</div>
<section id="meetups">
  {#each actualMeetups as meetup}
    <MeetupItem
      id={meetup.id}
      title={meetup.title}
      subtitle={meetup.subtitle}
      description={meetup.description}
      imageUrl={meetup.imageUrl}
      email={meetup.contactEmail}
      address={meetup.address}
      isFav={meetup.isFavorite}
      on:showDetails
      on:editMode />
  {/each}
</section>
