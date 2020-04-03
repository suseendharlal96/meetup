<script>
  import { onMount } from "svelte";
  import meetups from "./Meetups/meetup-store.js";
  import Header from "./UI/Header.svelte";
  import MeetupGrid from "./Meetups/MeetupGrid.svelte";
  import Button from "./UI/Button.svelte";
  import EditMeetup from "./Meetups/EditMeetup.svelte";
  import MeetupDetail from "./Meetups/MeetupDetail.svelte";

  let meetupsData = [];
  let loader = true;
  let form = false;
  let editMode = false;
  let showDetail = false;
  let formData;
  let detailId = null;
  let noData = true;

  // onMount(() => {
  loader = true;
  fetch("https://meetup-svelte-74ea9.firebaseio.com/meetups.json")
    .then(res => {
      if (!res.ok) {
        throw new Error("Failed");
      }
      return res.json();
    })
    .then(response => {
      const fetchedMeetups = [];
      loader = false;
      if (response) {
        for (const key in response) {
          fetchedMeetups.push({ ...response[key], id: key });
        }
        meetups.setMeetups(fetchedMeetups);
        noData = false;
      }
    })
    .catch(err => {
      loader = false;
    });
  // });

  function shouldFormShow(event) {
    form = event.detail.showForm;
    editMode = event.detail.showForm;
    loader = event.detail.loader;
    setTimeout(() => {
      loader = false;
      noData = false;
    }, 1000);
  }
  function showDetails(event) {
    detailId = event.detail;
    showDetail = true;
  }
  function editMeetup(event) {
    form = event.detail.form;
    editMode = event.detail.form;
    const editData = {
      id: event.detail.id,
      title: event.detail.title,
      subtitle: event.detail.subtitle,
      imageUrl: event.detail.imageUrl,
      description: event.detail.description,
      isFav: event.detail.isFav,
      address: event.detail.address,
      email: event.detail.email,
    };
    formData = editData;
    console.log(formData);
  }

  function toggleMode() {
    form = !form;
    editMode = false;
  }

  function filterFav(event) {
    meetups.filterFav(event.detail);
  }
</script>

<style>
  main {
    margin-top: 5rem;
  }
</style>

<Header />

<main>
  <!-- {#if !loader} -->
  {#if !showDetail}
    <Button type="button" on:click={toggleMode}>
      {!form ? 'Add New' : 'Cancel'}
    </Button>
    {#if form}
      {#if editMode}
        <EditMeetup {formData} on:add={shouldFormShow} />
      {:else}
        <EditMeetup on:add={shouldFormShow} />
      {/if}
    {:else if !loader}
      {#if !noData}
        <MeetupGrid
          meetups={$meetups}
          on:showDetails={showDetails}
          on:fav={filterFav}
          on:editMode={editMeetup} />
        <!-- {/if} -->
      {:else}
        <h2>No meetups found.Pls add one!</h2>
      {/if}
    {:else}
      <h1>Loading.....</h1>
    {/if}
  {:else}
    <MeetupDetail
      meetups={$meetups}
      {detailId}
      on:close={() => (showDetail = false)} />
  {/if}

</main>
