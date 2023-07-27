<script>
	import { Input, Label } from 'flowbite-svelte';
	import 'flowbite/dist/flowbite.css';
	import { Select, Dropdown, DropdownItem, ChevronDown } from 'flowbite-svelte';
	import { onMount } from 'svelte';
	import { TelInput, normalizedCountries } from 'svelte-tel-input';
	

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
		let selectedCountry=null;
		let isValid = false;
		


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
		<label for="phone" class="block text-sm font-medium text-gray-700 dark:text-white">Phone</label>
		<div class="relative mt-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700">
		  <select class="country-select {!isValid && 'invalid'} block w-full py-2.5 pl-3 pr-10 text-base border-transparent bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600" aria-label="Default select example" name="Country" bind:value={selectedCountry}>
			<option value={null} hidden={selectedCountry !== null}>Please select</option>
			{#each normalizedCountries as country (country.id)}
			  <option value={country.iso2} selected={country.iso2 === selectedCountry} aria-selected={country.iso2 === selectedCountry}>
				{country.iso2} (+{country.dialCode})
			  </option>
			{/each}
		  </select>
		 
		</div>
		<TelInput country={selectedCountry} bind:value={phone} class="form-select {!isValid && 'invalid'} mt-2 block w-full py-2.5 pl-3 pr-10 text-base border-transparent bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600" />
	  </div>
	  
	  <div class="mb-6">
		<Label for="website" class="mb-2">Website</Label>
		<Input type="url" id="website" bind:value={website} required />
	  </div>
	  <div class="mb-6">
		<label for="currency" class="block text-sm font-medium text-gray-700 dark:text-white">Currency:</label>
		<div class="relative mt-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700">
		  <select id="currency" bind:value={currencyCode} required class="block w-full py-2.5 pl-3 pr-10 text-base border-transparent bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600">
			{#each currencyOptions as currency}
			<option value={currency.value}>{currency.label}</option>
			{/each}
		  </select>
		
		</div>
	  </div>
	  
	  <div class="mb-6">
		<label for="preferredDateFormat" class="block text-sm font-medium text-gray-700 dark:text-white">Preferred Date Format</label>
		<div class="relative mt-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700">
		  <select id="preferredDateFormat" bind:value={preferredDateFormat} required class="block w-full py-2.5 pl-3 pr-10 text-base border-transparent bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600">
			<option value="pretty" selected>Pretty (e.g., 1 Day Ago/2 Week Ago, etc.)</option>
			<option value="dateOnly">Date only (e.g., 12/31/2020)</option>
			<option value="dateTime">Date and Time (e.g., 12/31/2020 15:00:00)</option>
			<option value="dateTime12h">Date and Time (e.g., 12/31/2020 03:00PM)</option>
		  </select>
		
		</div>
	  </div>
	  
	  
	  <div class="mb-6">
		<label for="timezone" class="block text-sm font-medium text-gray-700 dark:text-white">Timezone</label>
		<div class="relative mt-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700">
		  <select id="timezone" bind:value={selectedTimezone} required class="block w-full py-2.5 pl-3 pr-10 text-base border-transparent bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600">
			{#each reactiveTimezoneOptions as timezone }
			<option value={timezone.code}>{timezone.name}</option>
			{/each}
		  </select>
		  
		</div>
	  </div>
	  
	  <div class="mb-6">
		<button type="button" on:click={saveFormData} class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
		  update profile
		</button>
	  </div>

  </form>
</main>
  
  <style>
	.form-container {
	  margin-left: 550px;
	  width: 500px;
	 height: 200PX;
	  margin-top:-310px;
	}
	.container{
	 
	  width:600px;
	
	 
	}
  </style>