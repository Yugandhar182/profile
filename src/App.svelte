<script>
	import { Input, Label } from 'flowbite-svelte';
	import 'flowbite/dist/flowbite.css';
	import { Select, Dropdown, DropdownItem, ChevronDown } from 'flowbite-svelte';
	import { onMount } from 'svelte';	
	import { TelInput, normalizedCountries } from 'svelte-tel-input';
	import "intl-tel-input/build/css/intlTelInput.css";
	import intlTelInput from "intl-tel-input";
	import { afterUpdate } from "svelte";
	


		let id = "";
		let name = "";
		let address = {
		addressLine: "",
		cityName: "",
		regionName: "",
		postCode: "",
		countryCode: "",
		latitude: "",
		longitude: "",
	   };
	   let phone='';
		let website = "";
		let currencyCode = "gb";
		let preferredDateformat = "";
		let timeZone = "";
		let preferredCountryCode = "gb";
		let preferredCountries = [];
		let currencyOptions = [];
	    let selectedTimezone;
        let reactiveTimezoneOptions = [];
		let countryCode=null;
		let imageUrl = null;
		let isValid = false;
		let dataFetched = false;
		let countryFlags = [];
	
		 

		
	const fetchData = async () => {
	  try {
		const response = await fetch("https://api.recruitly.io/api/business/profile?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77");
		const data = await response.json();
		console.log(data);
	
		// Populate the form fields with the fetched data
		id = data.id;
		name = data.name;
		address = {
      addressLine: data.address.addressLine,
      cityName: data.address.cityName,
      regionName: data.address.regionName,
      postCode: data.address.postCode,
      countryCode: data.address.countryCode,
      latitude: data.address.latitude,
      longitude: data.address.longitude,
    };
	console.log(address.cityName);
	
	console.log(address.countryCode);
		phone = data.phone;
		console.log(phone);
		website = data.website;
		currencyCode = data.currencyCode.code;
		preferredDateformat = data.preferredDateFormat; 
		timeZone = data.timeZone;
		preferredCountryCode = data.preferredCountryCode;
		preferredCountries = [data.preferredCountries];
		dataFetched = true;
		return phone ;
	  } catch (error) {
		console.error("Error fetching data:", error);
	  }
	};
	
	// Fetch data on component mount
	onMount(() => {
	  fetchData();
	});
	async function saveFormData() {
  const Update = {
    id,
    name,
	address : {
      addressLine: address.addressLine,
      cityName: address.cityName,
      regionName:address.regionName,
      postCode:address.postCode,
      countryCode:address.countryCode,
      latitude:address.latitude,
      longitude:address.longitude,
    },
    phone,
    website,
    currencyCode: {
      code: currencyCode,
    },
    preferredDateformat,
    timeZone,
    preferredCountries: preferredCountries,
    preferredCountryCode,
  };

  console.log("Update data:", JSON.stringify(Update));
	  
	  // Send the updated data to the API here
	  try {
		const response = await fetch('https://api.recruitly.io/api/business/profile/save?apiKey=TEST45684CB2A93F41FC40869DC739BD4D126D77', {
		  method: "POST",
		  headers: {
			"Content-Type": "application/json",
		  },
		  body: JSON.stringify(Update),
		});
		const updatedData = await response.json();
		console.log("Updated data from API:", updatedData);
		alert("Business Profile Updated succesfully");
	  } catch (error) {
		console.error("Error updating data:", error);
	  }
	}

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

				 async function fetchImage() {
  const apiKey = "TEST45684CB2A93F41FC40869DC739BD4D126D77";
  const uniqueParam = Date.now(); // Generate a unique parameter using the current timestamp
  const apiUrl = `https://api.recruitly.io/api/business/profile?apiKey=${apiKey}&timestamp=${uniqueParam}`;

  try {
    const response = await fetch(apiUrl);
    const data = await response.json();
    // Access the imageUrl from the appLogo object
    imageUrl = data.logo.url;
  } catch (error) {
    console.error("Error fetching image:", error);
  }
}


	// Function to handle the logo click and trigger image upload
	function handleLogoClick() {
	  const inputElement = document.createElement("input");
	  inputElement.type = "file";
	  inputElement.accept = "image/*";
	  inputElement.addEventListener("change", uploadImage);
	  inputElement.click();
	}
  
	// Function to handle image upload
	async function uploadImage(event) {
  const apiKey = "TEST45684CB2A93F41FC40869DC739BD4D126D77";
  const uploadUrl = `https://api.recruitly.io/api/image/upload?apiKey=${apiKey}`;
  const formData = new FormData();
  formData.append("file", event.target.files[0]);
  formData.append("profilePic", "true");
  formData.append("type", "TENANT");

  try {
    const response = await fetch(uploadUrl, {
      method: "POST",
      headers: {
        Cookie: "SESSION=NDU1ZDRjNmUtNDg1ZC00NjVhLWJhNmItN2NlZmE4NzYxMWRm",
        "Cache-Control": "no-cache, no-store, must-revalidate", // Add the Cache-Control header
      },
      body: formData,
    });
    const data = await response.json();
    // The uploaded image URL should be available in 'data.url'
    imageUrl = data.logo.url;
	
  } catch (error) {
    console.error("Error uploading image:", error);
  }
}fetchImage();

async function getDefaultPhone() {
	  phone = await fetchData();
	  // Initialize the intlTelInput plugin
	  const inputElement = document.getElementById('phone');
	  intlTelInput(inputElement, {
	
	    DialCode:true, // Show the dial code in a separate input field
		
		initialCountry: address.countryCode,
		utilsScript:'https://cdnjs.cloudflare.com/ajax/libs/intl-tel-input/8.5.0/js/utils.js',
	  });
	}
  
	getDefaultPhone();
  
	


  </script>

{#if dataFetched}
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
		<img id="uploadedImage" src="{imageUrl}" alt="Logo" on:click={handleLogoClick} style="cursor: pointer;" />
		
	</div>
	<div class="mb-6">
		<Label for="company_name" class="mb-2">Company Name</Label>
		<Input type="text" id="name" bind:value={name} required />
		
	  </div>
	  <div class="mb-6">
		<Label for="address" class="mb-2">Address</Label>
		<Input type="text" id="address" bind:value={address.cityName} required />
   </div>
	
	  
   <div>
	
	<div class="mb-6">
	<label for="phone" class="mb-2" >Phone</label>
	<input type="tel" id="phone" bind:value={phone} inputmode="tel" class="form-select  block w-full py-2.5 pl-3 pr-10 text-base border border-gray-300 rounded-lg bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600" style="width:500px;" required />

  </div>
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
	  
	  <label for="preferredDateFormat" class="block text-sm font-medium text-gray-700 dark:text-white">Preferred Date Format</label>
	  <div class="relative mt-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700">
		<select id="preferredDateFormat" bind:value={preferredDateformat} required class="block w-full py-2.5 pl-3 pr-10 text-base border-transparent bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600">
		  <option value="default" >Pretty (e.g., 1 Day Ago/2 Week Ago, etc.)</option>
		  <option >Date only (e.g., 12/31/2020)</option>
		  <option >Date and Time (e.g., 12/31/2020 15:00:00)</option>
		  <option >Date and Time (e.g., 12/31/2020 03:00PM)</option>
		</select>
	  </div>
	  
	
	  
	  
	  <div class="mb-6">
		<label for="timezone" class="block text-sm font-medium text-gray-700 dark:text-white">Timezone</label>
		<div class="relative mt-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700">
		  <select id="timezone" bind:value={timeZone} required class="block w-full py-2.5 pl-3 pr-10 text-base border-transparent bg-transparent focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm dark:text-white dark:focus:ring-gray-700 dark:focus:border-gray-600">
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
{/if}

  
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
	.mb-6 {
 
   border-radius: 1.25rem; 
}
	
  </style>