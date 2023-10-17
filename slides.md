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
image: /github-pinned.png
---

<div class='h-full flex flex-col justify-center items-center'>

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

<div class='flex flex-col space-y-6 items-center'>
	<img class='h-20' src='/assets/spacedrive-logo.png' />
	<img class='h-100' src='/assets/spacedrive-app.webp' />
</div>

<!--
- At Spacedrive we are building an open-source file explorer designed to create and synchronise an index of all your files both locally and across cloud storage
- Using Rust for backend and web tech for frontend - TypeScript, React, etc.
- We have a lot of forms...
-->

---
layout: center
---

<div class='flex flex-row -gap-8'>
<img src='/assets/location-settings.png' class='h-120'>

<v-click>
<img src='/assets/tag-create.png' class='h-120 -ml-64'>
</v-click>

</div>

<v-click>
<div class='absolute inset-0 flex flex-row gap-4 justify-center items-center bg-black/20 backdrop-blur-[1px]'>
<img src='/assets/onboarding-library.png' class='h-100'>
<img src='/assets/onboarding-privacy.png' class='h-100'>
</div>
</v-click>

<!--
- Settings Forms

*click*

- Forms inside modals

*click*

- Multi-step wizard forms
- Par for the course, but you may take some pity on us since...
-->

---
class: text-center
layout: default
---

<div class='w-full h-full flex flex-col items-center justify-center transform scale-75'>
	<Tweet class='min-w-lg' id='1610812416207495174' />
	<Tweet class='min-w-lg' id='1255179383372836869' />
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

<div class='flex flex-row space-x-8 items-center'>

```tsx {all|2-3|10-19|20|6-9|all}
function LoginForm() {
	const [email, setEmail] = useState('')
	const [password, setPassword] = useState('')

	return (
		<form onSubmit={(e) => {
			e.preventDefault()
			console.log({ email, password })
		}}>
			<input
				name='email' type='email' required
				value={email}
				onChange={e => setEmail(e.target.value)}
			/>
			<input
				name='password' type='password' required
				value={password}
				onChange={e => setPassword(e.target.value)}
			/>
			<button type='submit' />
		</form>
	)
}
```

</div>

<!--
- Here's a basic form

*click*

- Each field gets its own `useState`

*click*

- Each input geta two-way bindings

*click*

- Submit button bc every form needs one

*click*

- Form has `onSubmit` handler with `preventDefault` to prevent page reloading
- Form values captured into callback from `useState`

*click*

- Gets the job done for basic forms, but is cumbersome and could go wrong
- Input names aren't validated, bindings could be hooked up incorrectly
-->

---
layout: center
---

- TODO: NextJS

```tsx
function LoginForm() {
	const [email, setEmail] = useState('')
	const [password, setPassword] = useState('')

	return (
		<form onSubmit={(e) => {
			e.preventDefault()
			console.log({ email, password })
		}}>
			<input
				name='email' type='email' required
				value={email}
				onChange={e => setEmail(e.target.value)}
			/>
			<input
				name='password' type='password' required
				value={password}
				onChange={e => setPassword(e.target.value)}
			/>
			<button type='submit' />
		</form>
	)
}
```

- TODO: Remix

<!--
- Keep in mind, we're just talking about client-controlled forms

*click*

- Some tools have their own ways of managing forms,
- What we'll talk about isn't necessarily mutually exclusive to them

*click*

- They won't be considered as part of this
-->

---
layout: center
---

<div class='flex flex-row space-x-4 items-center'>

```tsx
function LoginForm() {
	const [email, setEmail] = useState('')
	const [password, setPassword] = useState('')

	return (
		<form onSubmit={(e) => {
			e.preventDefault()
			console.log({ email, password })
		}}>
			<input
				name='email' type='email' required
				value={email}
				onChange={e => setEmail(e.target.value)}
			/>
			<input
				name='password' type='password' required
				value={password}
				onChange={e => setPassword(e.target.value)}
			/>
			<button type='submit' />
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

<!--
- So, where are we at?

*click*

- This evidently works alright for small forms
- Could get hairy with more fields and more logic eg. communicating with a server in onSubmit

*click*

- Also, not typesafe
- Form data being spread over each `useState` rather than centralised in one place
- What can we do about this? First thing we'll use is...
-->

---
layout: center
class: text-center
---

<img class='h-64' src='/assets/rhf-logo.png' />

<!--
- React Hook Form
- Library to help with form state management
- Builtin validation utilities
- Will help with typesafety later on 👀
-->

---
layout: center
clicks: 3
---

<div class='flex flex-row items-center space-x-8'>

<div>

```tsx {2-3|6-9|12-13,17-18|all} {at:0}
function LoginForm() {
	const [email, setEmail] = useState('')
	const [password, setPassword] = useState('')

	return (
		<form onSubmit={(e) => {
			e.preventDefault()
			console.log({ email, password })
		}}>
			<input
				type='email' required
				name='email' value={email}
				onChange={e => setEmail(e.target.value)}
			/>
			<input
				type='password' required
				name='password' value={password}
				onChange={e => setPassword(e.target.value)}
			/>
			<button type='submit' />
		</form>
	)
}
```

</div>

<div>

```tsx {1,4|7-9|12,16|all} {at:0}
import { useForm } from 'react-hook-form'

function LoginForm() {
	const form = useForm()

	return (
		<form onSubmit={
			form.handleSubmit((data) => console.log(data))
		}>
			<input
				type='email' required
				{...form.register('email')}
			/>
			<input
				type='password' required
				{...form.register('password')}
			/>
			<button type='submit' />
		</form>
	)
}
```

</div>

</div>

<!--
- Individual `useState` become single `useForm`

*click*

- `handleSubmit` wrapper calls `preventDefault` for us and collects all form fields into single object

*click*

- `name` prop and data bindings all handled by single `register` call

*click*

- Not sure about you, but I much prefer this form
- It's not a whole lot less code, but we're only just getting started 😉
-->

---
layout: center
---

<div class='flex flex-row items-center space-x-8'>

<div>

```tsx
import { useForm } from 'react-hook-form'

function LoginForm() {
	const form = useForm()

	return (
		<form onSubmit={
			form.handleSubmit((data) => console.log(data))
		}>
			<input
				type='email' required
				{...form.register('email')}
			/>
			<input
				type='password' required
				{...form.register('password')}
			/>
			<button type='submit' />
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
clicks: 4
---

<div class='flex flex-row items-center gap-8'>

<div>

```tsx {3-6|9|13-14|18-19,23-24|} {at:0}
import { useForm } from 'react-hook-form'

type Form = {
	email: string
	password: string
}

function LoginForm() {
	const form = useForm<Form>()'

	return (
		<form onSubmit={
			form.handleSubmit((data) => console.log(data))
//			                 ^? Form
		}>
			<input
				type='email' required
				{...form.register('email')}
//					          ^? 'email' | 'password'
			/>
			<input
				type='password' required
				{...form.register('password')}
//					          ^? 'email' | 'password'
			/>
			<button type='submit' />
		</form>
	)
}
```

</div>

<div class='flex flex-col items-center space-y-8'>

<div>

# Adding Types

<v-clicks at='0'>

- Generic is source of truth for whole form
- `data` has known structure
- Names used in `register` can be checked

</v-clicks>

</div>

</div>

</div>

<!--
- Declare shape of form in a single type
*click*
- Put it in `useForm`
- `handleSubmit` will receive correct types
- Field names used in `register` calls will be checked
- TAKEAWAY: Source of truth for type + source of truth for runtime (`useForm`) = dispersion of types through your whole app
- Not entirely reliable though
-->

---
layout: center
---

```tsx {|3-6|12-15|} {at:0}
import { useForm } from 'react-hook-form'

type Form = {
	count: number
}

function CountForm() {
	const form = useForm<Form>()

	return (
		<form onSubmit={form.handleSubmit(console.log)}>
			<input
				type='number' required
				{...form.register('count')}
			/>
			<button type='submit' />
		</form>
	)
}
```

<!--
Take this example:
- Single number field
*click*
- Input with `type='number'`
*click*
- According to TypeScript, this is fine
- This code actually has a bug
	- RHF uses input's `value` property by default
    - `value` is always a string
-->

---
layout: center
---

```tsx {12-17}
import { useForm } from 'react-hook-form'

type Form = {
	count: number
}

function LoginForm() {
	const form = useForm<Form>()

	return (
		<form onSubmit={form.handleSubmit(console.log)}>
			<input
				type='number' required
				{...form.register('count', {
					valueAsNumber: true
				})}
			/>
			<button type='submit' />
		</form>
	)
}
```

<!--
- Necessary to add `valueAsNumber` to force conversion
- This isn't typesafe at all!
  - Might forget to put `valueAsNumber`
- What if we could validate the data before submit handler is ran?
-->

---
layout: center
---

<p class='text-6xl text-center'>Zod</p>

<img src='/assets/zod-logo.svg' class='h-64' />

<!--
- Excellent library for validating data
- Not only validates data, also allows type inference from our validators
-->

---
layout: center
---

<div class='flex flex-row items-center gap-12'>

<img src='/assets/zod-logo.svg' class='h-64' />

<p class='text-6xl'>❤</p>️

<img src='/assets/rhf-logo.svg' class='h-56' />

</div>

<!--
- Define one validator, use it at compile time for type checking + runtime for validating fields
-->

---
layout: center
clicks: 2
---

<div class='flex flex-col items-center -translate-y-5'>

<div class='flex flex-row items-center gap-2 mb-12'>

<img src='/assets/zod-logo.svg' class='h-24' />

<p class='text-xl'>❤</p>️

<img src='/assets/rhf-logo.svg' class='h-20' />

</div>

<div class='flex flex-row items-center gap-16'>

<div class='scale-120'>

```tsx {1,5-9|1-2,5-9,12|all}
import { z } from 'zod'
import { useForm } from 'react-hook-form'
import { zodResovler } from '@hookform/resolvers'

const schema = z.object({
	email: z.string().email(),
	password: z.string().min(8)
})

// ...

const form = useForm<z.infer<typeof schema>>({
	resolver: zodResolver(schema)
})
```

</div>

<v-clicks at='-1'>

- Zod schema defines shape of form
- Form type is _inferred_ from schema
- `resolver` tells RHF to validate <br/> using schema

</v-clicks>

</div>

</div>

<!--
- First, we declare schema
  - Represents the shape of `handleSubmit` data + defines validations
  - Zod comes with a bunch of validation functions, so we can validate emails easily and enforce minimum lengths
*click*
- Second, we infer the type of the form _from_ the schema, effectively killing 2 birds with 1 stone
*click*
- Last, we set the schema as our form's 'resolver', meaning that it will be used for all validation
-->

---
layout: center
---

```tsx
import { z } from 'zod'
import { useForm } from 'react-hook-form'
import { zodResovler } from '@hookform/resolvers'

const schema = z.object({
	email: z.string().email(),
	password: z.string().min(8)
})

function LoginForm() {
	const form = useForm<z.infer<typeof schema>>({
		resolver: zodResolver(schema)
	})

	return (
		<form onSubmit={form.handleSubmit(console.log)}>
			<input type='email' {...form.register('email')} />
			<input type='password' {...form.register('password')} />
			<button type='submit' />
		</form>
	)
}
```

<!--
Here's what that looks like
- Schema replaces type
-->

---
layout: center
---

```tsx {|10-12}
import { z } from 'zod'
import { useForm } from 'react-hook-form'
import { zodResovler } from '@hookform/resolvers'

const schema = z.object({
	count: z.coerce().number()
})

function CountForm() {
	const form = useForm<z.infer<typeof schema>>({
		resolver: zodResolver(schema)
	})

	return (
		<form onSubmit={form.handleSubmit(console.log)}>
			<input type='number' {...form.register('count')} />
			<button type='submit' />
		</form>
	)
}
```

<!--
- In cases with number inputs, can use `z.coerce()` to convert string to number for us
  - Less typesafe than dedicated `<NumberInput />`, but...
  - `z.number()` would produce error if not used, preventing `onSubmit` for ever being called

- Great, we've got Zod and RHF working together, but...
*click*
- We've got code duplication on every form
- Wouldn't it be great to have a **custom hook** that can wrap `schema` in `zodResolver`, and also insert the generic for us?
- Well...
-->
---
layout: center
---

<div class='flex flex-row items-center gap-4'>

```tsx {10}
import { z } from 'zod'
import { useZodForm } from './useZodForm'

const schema = z.object({
	email: z.string().email(),
	password: z.string().min(8)
})

function LoginForm() {
	const form = useZodForm({ schema })

	return (
		<form onSubmit={form.handleSubmit(console.log)}>
			<input type='email' {..form..register('email')} />
			<input type='password' {...form.register('password')} />
			<button type='submit' />
		</form>
	)
}
```

```ts
import { useForm, UseFormProps } from 'react-hook-form'
import { zodResovler } from '@hookform/resolvers'
import { ZodSchema, z } from 'zod'

type UseZodFormProps<T extends ZodSchema> =
  Omit<UseFormProps<z.infer<T>>, 'resolver'>
  & { schema: T }

export function useZodForm<T extends ZodSchema>({
	schema,
	...props
}: UseZodFormProps<T>) {
	return useForm({
		...props,
		resolver: zodResolver(schema)
	})
}
```

</div>

<!--
- How about this?
  - Calls `useForm` and automatically provides `schema` as `resolver`
- These types may look scary, but don't worry!
  - `UseZodFormProps` is just the type that `useForm` expects, but removes `resolver` since we're providing it ourselves, and adds `schema`
  - `T` represents the type of the schema we provide - without it, types wouldn't be passed through to things like `handleSubmit`
-->

---
layout: center
---

<div class='flex flex-row items-center gap-4'>

```tsx
import { z } from 'zod'
import { useZodForm } from './useZodForm'

const schema = z.object({
	email: z.string().email(),
	password: z.string().min(8)
})

function LoginForm() {
	const form = useZodForm({ schema })'

	return (
		<form onSubmit={form.handleSubmit(console.log)}>
			<input type='email' {..form..register('email')} />
			<input type='password' {...form.register('password')} />
			<button type='submit' />
		</form>
	)
}
```

<div>

## Zod + RHF

<br/>

- All form logic hidden behind `useZodForm`
- No manual types
- `handleSubmit` always receives correct data

<v-click>

<div class='text-red-500 absolute translate-4'>

## What if things go wrong?!

</div>

</v-click>

</div>

</div>

<!--
- So here's where we are so far
  - All form logic hidden behind hook
  - No more manual types
  - What I think a good TS experience should be
    - Types should be declared once + flow through
- ...
*click*
- What if validation doesn't succeed?
- RHF can help here too
-->

---
layout: center
---

<div class='flex flex-col items-center'>

# Displaying Errors

```tsx {4,10,14|}
function LoginForm() {
	const form = useZodForm({ schema })'

	const { errors } = form.formState'

	return (
		<form onSubmit={form.handleSubmit(console.log)}>
			<div>
				<input type='email' {..form.register('email')} />
				{errors.email && <p>{errors.email.message}</p>}
			</div>
			<div>
				<input type='password' {...form.register('password')} />
				{errors.password && <p>{errors.email.password}</p>}
			</div>
			<button type='submit' />
		</form>
	)
}
```

</div>

<!--
- Gives us handy `errors` object we can then _use_ to display errors for each field
- btw `errors` is typesafe - TS will yell at us if we try get errors for a non-existent field
*click*
- This gets repetitive though
- Better solution is to group the input and error into a separate component
-->

---
layout: center
---

<div class='flex flex-row items-center gap-8'>

```tsx {all|none} {at:0}
import { Input } from './Input'

function LoginForm() {
	const form = useZodForm({ schema })'

	return (
		<form onSubmit={form.handleSubmit(console.log)}>
			<Input type='email' {...form.register('email')} />
			<Input type='password' {...form.register('password')} />
			<button type='submit' />
		</form>
	)
}
```

```tsx {|8,13} {at:0}
import { ComponentProps, forwardRef } from 'react'
import { useFormContext } from 'react-hook-form'

export const Input = forwardRef<
	HTMLInputElement,
	ComponentProps<'input'>
>((props, ref) => {
  const form = ??'

  return (
    <div>
      <input {...props} ref={ref} />
      {??.error && <p>{??.error.message}</p>}
    </div>
  )'
})'
```

</div>

<!--
- Something like this
- `Input` renders both the input _and_ the error if there is one
*click*
- But where do we get `form` from?
  - Could pass `form` to every `Input`, but that would get annoying
  - If only there was a way to make a value available to all of a component's children...
  - Wait a minute - we do have that!
-->

---
layout: center
clicks: 4
---

<div class="flex flex-row items-center gap-8">

<div class="flex flex-col items-center gap-4">

# Context to the Rescue!
```tsx {|1,8,14|,||8-9,13-14} {at:0}
import { FormProvider } from "react-hook-form"
import { Input } from "./Input"

function LoginForm() {
	const form = useZodForm({ schema })

	return (
		<FormProvider {...form}>
			<form onSubmit={form.handleSubmit(console.log)}>
				<Input type="email" {...form.register("email")} />
				<Input type="password" {...form.register("password")} />
				<button type="submit" />
			</form>
		</FormProvider>
	)
}
```

</div>

```tsx {|2,13|14-18,23|all|none} {at:0}
import { ComponentProps, forwardRef } from 'react'
import { useFormContext } from 'react-hook-form'

export interface InputProps
    extends ComponentProps<'input'> {
    name: string
}

export const Input = forwardRef<
	HTMLInputElement,
	InputProps
>((props, ref) => {
  const form = useFormContext()

  const field = form.getFieldState(
  	props.name,
   	form.formState
  )

  return (
    <div>
      <input {...props} ref={ref} />
      {field.error && <p>{field.error.message}</p>}
    </div>
  )
})
```

</div>

<!--
- Context!
*click*
- RHF gives us this `FormProvider` we can consume with `useFormContext`
*click*
- Can then get the field's state and render the error!

*click*
- Notice that we override the `name` field of the component's props, since `name` is now required for use in `getFieldState`.

- While we're here, lets do something about...
*click*
- These, since every form is going to need them.
-->

---
layout: center
clicks: 2
---

<div class='flex flex-row items-center gap-8'>

```tsx {1,7,11|none} {at:0}
import { Form } from './Form'

function LoginForm() {
	const form = useZodForm({ schema })

	return (
		<Form form={form} onSubmit={form.handleSubmit(console.log)}>
			<Input type='email' {...form.register('email')} />
			<Input type='password' {...form.register('password')} />
			<button type='submit' />
		</Form>
	)
}
```

```tsx {all|17-19} {at:0}
import { ComponentProps } from 'react'
import {
  FieldValues,
  FormProvider,
  UseFormReturn,
} from 'react-hook-form'

export interface FormProps<T extends FieldValues>
	extends ComponentProps<'form'> {
	form: UseFormReturn<T>
}

export function Form<T extends FieldValues>({
  form, ...props
}: FormProps<T>) {
  return (
	  <FormProvider {...form}>
	    <form {...props} />
	  </FormProvider>
  )
}
```

</div>

<!--
- Let's put them in a dedicated component
- We now provide our form via the `form` prop
- It's propagated to all child fields
- Another thing I like to do is head down...
*click*
- here, and wrap all the children in a...
-->

---
layout: center
---

<div class='flex flex-row items-center gap-8'>

```tsx {,} {at:0}
import { Form } from './Form'

function LoginForm() {
	const form = useZodForm({ schema })

	return (
		<Form form={form} onSubmit={form.handleSubmit(console.log)}>
			<Input type='email' {...form.register('email')} />
			<Input type='password' {...form.register('password')} />
			<button type='submit' />
		</Form>
	)
}
```

```tsx {17-23} {at:0}
import { ComponentProps } from 'react'
import {
  FieldValues,
  FormProvider,
  UseFormReturn,
} from 'react-hook-form'

export interface FormProps<T extends FieldValues>
	extends ComponentProps<'form'> {
	form: UseFormReturn<T>
}

export function Form<T extends FieldValues>({
  form, children, ...props
}: FormProps<T>) {
  return (
    <FormProvider {...form}>
      <form {...props}>
        <fieldset disabled={form.formState.isSubmitting}>
          {children}
        </fieldset>
      </form>
    </FormProvider>
  )
}
```

</div>

<!--
- `<fieldset>`, as it has a special property
- If `disabled` is `true` then all form elements will also be `disabled`
  - Won't be able to interact with buttons and inputs
  - `disabled` css selector will be applied
- While form is submitting, this disabled state will propagate
 - If `handleSubmit` contains an async function, it will wait to resolve
- All in all...
*click*
-->

---
layout: center
---

<div class='flex flex-row items-center gap-8'>

<div>

```tsx
import { useZodForm } from './useZodForm'
import { Input } from './Input'
import { Form } from './Form'

const schema = z.object({
	email: z.string().email(),
	password: z.string().min(8)
})

function LoginForm() {
	const form = useZodForm({ schema })

	return (
		<Form form={form} onSubmit={form.handleSubmit(console.log)}>
			<Input type='email' {...form.register('email')} />
			<Input type='password' {...form.register('password')} />
			<button type='submit' />
		</Form>
	)
}
```

</div>

<img src='/assets/login-no-labels.png' class='h-58 rounded-md' />

</div>

<!--
- Our form code is looking pretty sleek!
- This form is missing something though...
-->

---
layout: center
---

<div class='flex flex-col items-center'>

# Labels!

<div class='flex flex-row items-center gap-8'>

<div>

```tsx {all|7,11}
function LoginForm() {
	const form = useZodForm({ schema })

	return (
		<Form form={form} onSubmit={form.handleSubmit(console.log)}>
			<Input
			  type='email' label='Email'
			  {...form.register('email')}
			/>
			<Input
			  type='password' label='Password'
			  {...form.register('password')}
			/>
			<button type='submit' />
		</Form>
	)
}
```

</div>

<img src='/assets/login-with-labels.png' class='h-58 rounded-md' />

</div>

</div>

<!--
# Labels!

- We can just pass them as a prop to our inputs...
*click*
-->

---
layout: center
---

<div class='flex flex-row items-center gap-8'>

<div>

```tsx {7,11}
function LoginForm() {
	const form = useZodForm({ schema })

	return (
		<Form form={form} onSubmit={form.handleSubmit(console.log)}>
			<Input
			  type='email' label='Email'
			  {...form.register('email')}
			/>
			<Input
			  type='password' label='Password'
			  {...form.register('password')}
			/>
			<button type='submit' />
		</Form>
	)
}
```

</div>

```tsx {4,20-23}
export interface InputProps
	extends ComponentProps<'input'> {
	name: string
	label: string
}

export const Input = forwardRef<
	HTMLInputElement,
	InputProps
>((props, ref) => {
	const form = useFormContext()

	const field = form.getFieldState(
		props.name,
		form.formState
	)

	return (
		<div>
			<label htmlFor={props.name}>
				{props.label}
			</label>
			<input {...props} id={props.name} ref={ref} />
			{field.error && <p>{field.error.message}</p>}
		</div>
	)
})
```

</div>

<!--
- And then render the label above the input
- Best practice to include the label so we'll make it required
- Need to set `htmlFor` and `id` to link the label to the input
- But...
*click*
-->

---
layout: center
---

<div class='flex flex-row items-center gap-8'>

<div>

## What If `name` Isn't Unique?

<br/>

- Could have multiple forms on one page
- `id` is supposed to be unique on the whole page

</div>

```tsx
export interface InputProps
	extends ComponentProps<'input'> {
	name: string
	label: string
}

export const Input = forwardRef<
	HTMLInputElement,
	InputProps
>((props, ref) => {
	const form = useFormContext()

	const field = form.getFieldState(
		props.name,
		form.formState
	)

	return (
		<div>
			<label htmlFor={props.name}>
				{props.label}
			</label>
			<input {...props} id={props.name} ref={ref} />
			{field.error && <p>{field.error.message}</p>}
		</div>
	)
})
```

</div>

<!--
- What if `name` _isn't_ unique?
- This system doesn't stop us having multiple forms on one page
- `id` is supposed to be unique on the whole page
- React gives us a solution...
*click*
-->

---
layout: center
---

<div class='flex flex-col items-center gap-8'>

## `useId`

```tsx
import { useId } from "react"

export const Input = forwardRef<
	HTMLInputElement,
	InputProps
>((props, ref) => {
	...

	const id = useId()

	return (
		<div>
			<label htmlFor={id}>
				{props.label}
			</label>
			<input {...props} id={id} ref={ref} />
			{field.error && <p>{field.error.message}</p>}
		</div>
	)
})
```

</div>

<!--
- `useId`
- One of those React features that you may not have heard of
- Generates a unique ID that is stable across rerenders
  - Doesn't change between when component mounts and unmounts
- Verifiably unique, as opposed to `name`
-->

---
layout: center
---

<div class='flex flex-row items-center gap-8'>

<div>

```tsx {all|7,11}
function LoginForm() {
	const form = useZodForm({ schema })

	return (
		<Form form={form} onSubmit={form.handleSubmit(console.log)}>
			<Input
			  type='email' label='Email'
			  {...form.register('email')}
			/>
			<Input
			  type='password' label='Password'
			  {...form.register('password')}
			/>
			<button type='submit' />
		</Form>
	)
}
```

</div>

<img src='/assets/login-with-labels.png' class='h-58 rounded-md' />

</div>
