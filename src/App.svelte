
<script>
	import CompanyProfileForm from './CompanyprofileForm.svelte';


  </script>
  


  <main class="container">
	<CompanyProfileForm
	  name:{name}
	  address:{address}
	  phone:{phone}
	  website:{website}
	  currencyCode:{currencyCode}
	  preferredDateformat:{preferredDateformat}
	  timeZone:{timeZone}
	  preferredCountryCode:{preferredCountryCode}
	  preferredCountries:{preferredCountries}
	  imageUrl:{imageUrl}
	  selected:{selected}
	/>
  </main>

  