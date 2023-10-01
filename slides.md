---
class: text-center
highlighter: shiki
lineNumbers: false
title: Building Type-Safe Forms in React
---

# Building Type-Safe Forms in React

## Brendan Allan

<!--
Hey everyone! It's so great to be here.
Thank you all for coming to listen to me talk about forms of all things
-->

---
layout: image-right
image: ./assets/github-pinned.png
---

<div class="h-full flex flex-col justify-center items-center">

# Who am I?

### Brendan Allan

<br />

- West Australian
- Rust and TypeScript enjoyer
- Open source maintainer
- Type safety enthusiast
- Software Engineer at Spacedrive

</div>

<!--
- My name is Brendan Allan
- I'm from the other side of the country
- Work in Rust and TypeScript
- OSS maintainer
- Type safety enthusiast - just ask work colleagues
- Software Engineer at Spacedrive - the company that is the reason I dropped out of university last year
-->

---
layout: center
---

<!-- We're building an open-source file browser with a React-powered UI and Rust backend that generates and synchronises an index of all your files across all your devices -->

<div class="flex flex-col space-y-6 items-center">
	<img class="h-20" src="/assets/spacedrive-logo.png" />
	<img class="h-100" src="/assets/spacedrive-app.webp" />
</div>

<!--
- At Spacedrive we are building an open-source file explorer designed to create and synchronise an index of all your files both locally and across cloud storage
- Using Rust for backend and web tech for frontend - TypeScript, React, etc.
- We have a lot of forms
  - Settings, datacreation, forms in update modals, onboarding
-->

---

# Spacedrive Forms

- Location settings
- Onboarding form

---
class: text-center
layout: default
---

<div class="w-full h-full flex flex-col items-center justify-center transform scale-75">
	<Tweet class="min-w-lg" id="1610812416207495174" />
	<Tweet class="min-w-lg" id="1255179383372836869" />
</div>

<!--
- Forms have a bad rap
- Managing state, field validation, submitting can be annoying
  - Especially when doing client processing for better UX
  - Hard to have good DX
-->

---
layout: center
---

<div class="flex flex-row space-x-8 items-center">

```tsx {all|2-3|10-19|20|6-9|all}
function LoginForm() {
	const [email, setEmail] = useState("");
	const [password, setPassword] = useState("");

	return (
		<form onSubmit={(e) => {
			e.preventDefault();
			console.log({ email, password })
		}}>
			<input
				name="email" type="email" required
				value={email}
				onChange={e => setEmail(e.target.value)}
			/>
			<input
				name="password" type="password" required
				value={password}
				onChange={e => setPassword(e.target.value)}
			/>
			<button type="submit" />
		</form>
	)
}
```

</div>
---
layout: center
---

- TODO: NextJS

```tsx
function LoginForm() {
	const [email, setEmail] = useState("");
	const [password, setPassword] = useState("");

	return (
		<form onSubmit={(e) => {
			e.preventDefault();
			console.log({ email, password })
		}}>
			<input
				name="email" type="email" required
				value={email}
				onChange={e => setEmail(e.target.value)}
			/>
			<input
				name="password" type="password" required
				value={password}
				onChange={e => setPassword(e.target.value)}
			/>
			<button type="submit" />
		</form>
	)
}
```

- TODO: Remix

---
layout: center
---

<div class="flex flex-row space-x-4 items-center">

```tsx
function LoginForm() {
	const [email, setEmail] = useState("");
	const [password, setPassword] = useState("");

	return (
		<form onSubmit={(e) => {
			e.preventDefault();
			console.log({ email, password })
		}}>
			<input
				name="email" type="email" required
				value={email}
				onChange={e => setEmail(e.target.value)}
			/>
			<input
				name="password" type="password" required
				value={password}
				onChange={e => setPassword(e.target.value)}
			/>
			<button type="submit" />
		</form>
	)
}
```

<span>
<v-clicks>

- Works fine for small forms 🤷
- Not typesafe 😡

</v-clicks>
</span>

</div>

---
layout: center
class: text-center
---


<img class="h-64" src="/assets/rhf-logo.png" />



---
layout: center
---

<div class="flex flex-row items-center space-x-8">

<div>

```tsx {2-3|7-8|12-13,17-18|all} {at:0}
function LoginForm() {
	const [email, setEmail] = useState("");
	const [password, setPassword] = useState("");

	return (
		<form onSubmit={(e) => {
			e.preventDefault();
			console.log({ email, password })
		}}>
			<input
				type="email" required
				name="email" value={email}
				onChange={e => setEmail(e.target.value)}
			/>
			<input
				type="password" required
				name="password" value={password}
				onChange={e => setPassword(e.target.value)}
			/>
			<button type="submit" />
		</form>
	)
}
```

</div>

<div>

```tsx {1,4|8|12,16|all} {at:0}
import { useForm } from "react-hook-form";

function LoginForm() {
	const form = useForm();

	return (
		<form onSubmit={
			form.handleSubmit((data) => console.log(data))
		}>
			<input
				type="email" required
				{...register("email")}
			/>
			<input
				type="password" required
				{...register("password")}
			/>
			<button type="submit" />
		</form>
	)
}
```

</div>

</div>

---
layout: center
---

<div class="flex flex-row items-center space-x-8">

<div>

```tsx
import { useForm } from "react-hook-form";

function LoginForm() {
	const form = useForm();

	return (
		<form onSubmit={
			form.handleSubmit((data) => console.log(data))
		}>
			<input
				type="email" required
				{...register("email")}
			/>
			<input
				type="password" required
				{...register("password")}
			/>
			<button type="submit" />
		</form>
	)
}
```

</div>

<div>

<v-clicks>

- Scales much better
- Still not typesafe 😡

</v-clicks>

</div>

</div>

<!--
What do we think of this?
- Scales much better
  - Don't need to add more state storage
  - All data gets passed through single `handleSubmit`
- Still not typesafe though
-->

---
layout: center
---

<div class="flex flex-row items-center gap-8">

<div>

```tsx {3-6|9|13-14|18-19,23-24|} {at:0}
import { useForm } from "react-hook-form";

type Form = {
	email: string;
	password: string;
}

function LoginForm() {
	const form = useForm<Form>();

	return (
		<form onSubmit={
			form.handleSubmit((data) => console.log(data))
//			                 ^? Form
		}>
			<input
				type="email" required
				{...register("email")}
//					          ^? "email" | "password"
			/>
			<input
				type="password" required
				{...register("password")}
//					          ^? "email" | "password"
			/>
			<button type="submit" />
		</form>
	)
}
```

</div>

<div class="flex flex-col items-center space-y-8">

<div>

# Adding Types

<v-clicks at="0">

- Generic is source of truth for whole form
- `data` has known structure
- Names used in `register` can be checked

</v-clicks>

</div>

</div>

</div>

<!--
- Declare shape of form in a single type
- Putting it
- `handleSubmit` will receive correct types
- Field names used in `register` calls will be checked
- TAKEAWAY: Source of truth for type + source of truth for runtime (`useForm`) = dispersion of types through your whole app
- Not entirely reliable though
-->

---
layout: center
---

```tsx {|3-6|12-15} {at:0}
import { useForm } from "react-hook-form";

type Form = {
	count: number;
}

function CountForm() {
	const form = useForm<Form>();

	return (
		<form onSubmit={form.handleSubmit(console.log)}>
			<input
				type="number" required
				{...register("count")}
			/>
			<button type="submit" />
		</form>
	)
}
```

<!--
Take this example:
- Single number filed
- Input with `type="number"`
- According to TypeScript, this is fine
- Actually, the `value` received for `count` will be a string
-->

---
layout: center
---

```tsx {12-16}
import { useForm } from "react-hook-form";

type Form = {
	count: number;
}

function LoginForm() {
	const form = useForm<Form>();

	return (
		<form onSubmit={form.handleSubmit(console.log)}>
			<input
				type="number" required
				valueAsNumber
				{...register("count")}
			/>
			<button type="submit" />
		</form>
	)
}
```

<!--
- Necessary to add `valueAsNumber` to force conversion
- This isn't typesafe at all!
  - Might forget to put `valueAsNumber`
- This calls for some validation...
-->

---
layout: center
---

<p class="text-6xl text-center">Zod</p>

<img src="/assets/zod-logo.svg" class="h-64" />

<!--
- Excellent library for validating data
- Not only validates data, also allows type inference from our validators
-->

---
layout: center
---

<div class="flex flex-row items-center gap-12">

<img src="/assets/zod-logo.svg" class="h-64" />

<p class="text-6xl">❤</p>️

<img src="/assets/rhf-logo.svg" class="h-56" />

</div>

<!--
- Define one validator, use it at compile time for type checking + runtime for validating fields
-->

---
layout: center
---

```tsx {1,5-9|1-2,12|1,3,5-9,13|}
import { z } from "zod"
import { useForm } from "react-hook-form"
import { zodResovler } from "@hookform/resolvers"

const schema = z.object({
	email: z.string().email(),
	password: z.string().min(8)
})

// ...

const form = useForm<z.infer<typeof schema>>({
	resolver: zodResolver(schema)
});
```

<!--
- First, we declare schema
  - Represents the shape of `handleSubmit` data + defines validations
  - Zod comes with a bunch of validation functions, so we can validate emails easily and enforce minimum lengths
- Second, we infer the type of the form _from_ the schema, effectively killing 2 bird with 1 stone
- Last, we set the schema as our form's 'resolver', meaning that it will be used for all validation
-->

---
layout: center
---

```tsx
import { z } from "zod"
import { useForm } from "react-hook-form"
import { zodResovler } from "@hookform/resolvers"

const schema = z.object({
	email: z.string().email(),
	password: z.string().min(8)
})

function LoginForm() {
	const form = useForm<z.infer<typeof schema>>({
		resolver: zodResolver(schema)
	});

	return (
		<form onSubmit={form.handleSubmit(console.log)}>
			<input
				type="email"
				{...register("email")}
			/>
			<input
				type="password"
				{...register("password")}
			/>
			<button type="submit" />
		</form>
	)
}
```

---
layout: center
---

```tsx {|10-12}
import { z } from "zod"
import { useForm } from "react-hook-form"
import { zodResovler } from "@hookform/resolvers"

const schema = z.object({
	count: z.coerce().number()
})

function CountForm() {
	const form = useForm<z.infer<typeof schema>>({
		resolver: zodResolver(schema)
	});

	return (
		<form onSubmit={form.handleSubmit(console.log)}>
			<input
				type="number"
				{...register("count")}
			/>
			<button type="submit" />
		</form>
	)
}
```

<!--
- Here we don't need to remember `valueAsNumber`
- We can treat `<input>` with `type="number"` as though they return strings

- Great, we've got Zod and RHF working together
- We've got code duplication on every form
- Wouldn't it be great to have a **custom hook** that can wrap `schema` in `zodResolver`, and also insert the generic for us?
-->

---

```tsx
```
