<script>
	import { Input, Label } from 'flowbite-svelte';
	import 'flowbite/dist/flowbite.css';
	import { Select } from 'flowbite-svelte';
	import { onMount } from 'svelte';

		let name='';
		let address='';
		let phone='';
		let website='';
		let currencyCode='';
		let preferredDateFormat='';
		let timeZone='';
		let currencyOptions = [];
	    let selectedTimezone;
        let reactiveTimezoneOptions = [];
		let selectedCurrency = '';
		


	async function fetchDefaultValues() {
    try {
      const response = await fetch('https://api.recruitly.io/api/business/profile?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77');
      const data = await response.json();

      // Update form fields with the retrieved data
	  console.log(data);
      name = data.name;
      address = data.address.cityName;
      phone = data.phone;
      website = data.website;
      currencyCode = data.currencyCode.name;
     
      timeZone = data.timeZone;
    } catch (error) {
      console.error('Error fetching default values:', error);
    }
  }

  // Call the fetchDefaultValues function when the component is mounted
  onMount(fetchDefaultValues);


 
  

 

  onMount(async () => {
    try {
      const response = await fetch('https://api.recruitly.io/api/lookup/currency?apiKey=TEST27306FA00E70A0F94569923CD689CA9BE6CA');
      const responseData = await response.json();

 

      if (Array.isArray(responseData.data)) {
        // Map the response data to the format required for options
        currencyOptions = responseData.data.map((currency) => ({
          value: currency.code,
          label: currency.name,
        }));
      } else {
        console.error('Invalid currency data format:', responseData);
      }
    } catch (error) {
      console.error('Error fetching currency options:', error);
    }
  });


				fetch("https://api.recruitly.io/api/lookup/timezone?apiKey=TEST27306FA00E70A0F94569923CD689CA9BE6CA")
				  .then(response => {
				    if (!response.ok) {
				      throw new Error("Failed to fetch timezone data");
				       }
				   return response.json();
				  })
				   .then(responseData => {
				      console.log("API response:", responseData);
				       if (!Array.isArray(responseData.data)) {
				       throw new Error("Invalid timezone data");
				      }
				    reactiveTimezoneOptions = responseData.data.map((timezone, index) => ({
				    ...timezone,
				      id: index + 1,
				      }));
				     selectedTimezone = reactiveTimezoneOptions.length > 0 ? reactiveTimezoneOptions[0].code : null;
				     })
				    .catch(error => {
			  	  console.log("Error fetching timezone data:", error);
				 });


				 async function saveFormData() {
    try {
      const response = await fetch('https://api.recruitly.io/api/business/profile/save?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          name,
          address,
          phone,
          website,
          currencyCode,
          preferredDateFormat,
          timeZone,
        }),
      });

      if (!response.ok) {
        console.error('Failed to save form data:', response);
        return;
      }

      console.log('Form data saved successfully!');
    } catch (error) {
      console.error('Error saving form data:', error);
    }
  }


  </script>

  <main class="container">
  <form class="form-container">
	<div class="grid gap-6 mb-6 md:grid-cols-1">
	  <div>
		<Label for="company_profile" class="mb-1">Company Profile</Label>
	    <hr>
	  </div>
	</div>
	<div class="mb-6">
	  <Label for="company_logo" class="mb-2">Company Logo</Label>
	  <img src=https://s3-eu-west-1.amazonaws.com/recruitly-public/334fbbc8-6ab1-49d1-bede-5ead2a0b1a56/8abcc976-bf3d-438e-9485-e82be62db9fb.png />
	</div>
	<div class="mb-6">
		<Label for="company_name" class="mb-2">Company Name</Label>
		<Input type="text" id="name" bind:value={name} required />
	  </div>
	  <div class="mb-6">
		<Label for="address" class="mb-2">Address</Label>
		<Input type="text" id="address"  bind:value={address} required />
	  </div>
	  <div class="mb-6">
		<Label for="phone" class="mb-2">Phone</Label>
		<Input type="tel" id="phone" placeholder="Phone" bind:value={phone} required />
	  </div>
	  <div class="mb-6">
		<Label for="website" class="mb-2">Website</Label>
		<Input type="url" id="website" bind:value={website} required />
	  </div>
	  <div class="mb-6">
		<label for="currency" class="form-label">Currency:</label>
    <select class="form-select" id="currency" bind:value={currencyCode} required>
    {#each currencyOptions as currency}
    <option value={currency.value}>{currency.label}</option>
    {/each}
    </select>
	  </div>
	  <div class="mb-6">
		<label for="date_format" class="mb-2">Preferred Date Format</label>
		<select id="preferredDateFormat" bind:value={preferredDateFormat} required>
		  <option value="pretty" selected>Pretty (e.g., 1 Day Ago/2 Week Ago, etc.)</option>
		  <option value="dateOnly">Date only (e.g., 12/31/2020)</option>
		  <option value="dateTime">Date and Time (e.g., 12/31/2020 15:00:00)</option>
		  <option value="dateTime12h">Date and Time (e.g., 12/31/2020 03:00PM)</option>
		</select>
	  </div>
	  
	  <div class="mb-6">
		<label for="timezone" class="form-label">Timezone</label>
        <select class="form-select" id="timezone" bind:value={selectedTimezone} required>
       {#each reactiveTimezoneOptions as timezone (timezone.id)}
       <option value={timezone.code}>{timezone.name}</option>
       {/each}
	   </select>
	  </div>
	  <div class="mb-6">
		<button type="button" on:click={saveFormData} class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
		  Save
		</button>
	  </div>

  </form>
</main>
  
  <style>
	.form-container {
	  margin-left: 550px;
	  width: 400px;
	 height: 400PX;
	  margin-bottom:190px;
	}
	.container{
	 
	  width:600px;
	
	}
  </style>