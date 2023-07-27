<script>
	import { Input, Label, Helper, Button, Checkbox, A } from 'flowbite-svelte';
	import 'flowbite/dist/flowbite.css';
  </script>
  
  <form>
	<div class="grid gap-6 mb-6 md:grid-cols-1">
	  <div>
		<Label for="first_name" class="mb-1">First name</Label>
		<Input type="text" id="first_name" placeholder="John" required  />
	  </div>
	 </div>
	<div class="mb-6">
	  <Label for="email" class="mb-2">Email address</Label>
	  <Input type="email" id="email" placeholder="john.doe@company.com" required />
	</div>
	<div class="mb-6">
	  <Label for="password" class="mb-2">Password</Label>
	  <Input type="password" id="password" placeholder="•••••••••" required />
	</div>
	<div class="mb-6">
		<Label for="password" class="mb-2">Password</Label>
		<Input type="password" id="password" placeholder="•••••••••" required />
	  </div>
	  <div class="mb-6">
		<Label for="password" class="mb-2">Password</Label>
		<Input type="password" id="password" placeholder="•••••••••" required />
	  </div>
	  <div class="mb-6">
		<Label for="password" class="mb-2">Password</Label>
		<Input type="password" id="password" placeholder="•••••••••" required />
	  </div>
	<div class="mb-6">
	  <Label for="confirm_password" class="mb-2">Confirm password</Label>
	  <Input type="password" id="confirm_password" placeholder="•••••••••" required />
	</div>
	<Checkbox class="mb-6 space-x-1" required>I agree with the <A href="/" class="text-primary-700 dark:text-primary-600 hover:underline">terms and conditions</A>.</Checkbox>
	<Button type="submit">Submit</Button>
  </form>