<script>
	import { Button, Form, FormGroup, Label, Input, FormFeedback, Alert } from '@sveltestrap/sveltestrap';
	import Ajv from 'ajv';

	let id = '';
	let name = '';
	let type = '';
	let errors = {
		id: '',
		name: '',
		type: ''
	};
	let showAlert = false
	let alertMessage = ''

	const ajv = new Ajv({ allErrors: true });

	// JSON 스키마 정의
	const schema = {
		type: 'object',
		properties: {
			id: { type: 'string', minLength: 1, maxLength: 5 },
			name: { type: 'string', minLength: 1 },
			type: { type: 'string', minLength: 1 },
		},
		required: ['id', 'name', 'type'],
	};

	function validateField(fieldName, value) {
		// 단일 필드 유효성 검사
		const validate = ajv.compile({
			type: 'object',
			properties: {
				[fieldName]: schema.properties[fieldName],
			},
			required: schema.required.includes(fieldName) ? [fieldName] : [],
		});

		const valid = validate({ [fieldName]: value });
		if (!valid && validate.errors) {
			errors[fieldName] = validate.errors[0].message;
		} else {
			errors[fieldName] = '';
		}
	}

	function saveForm() {
		// 전체 폼 유효성 검사
		const validate = ajv.compile(schema);
		const valid = validate({ id, name, type });

		// 모든 필드의 오류 메시지를 초기화
		errors = { id: '', name: '', type: '' };

		if (!valid && validate.errors) {
			// 각 필드에 대한 오류 메시지를 설정
			console.log(validate.errors)
			validate.errors.forEach(error => {
				// error.instancePath는 필드 이름 앞에 슬래시('/')가 붙어 있습니다.
				const fieldName = error.instancePath.substring(1);
				errors[fieldName] = error.message;
			});
			console.error("Form validation failed:", validate.errors);
		} else {
			console.log("Form is valid. Submitting data:", { id, name, type });
			alertMessage = "Data saved successfully!";
			showAlert = true; // Alert 표시
			setTimeout(() => showAlert = false, 3000); // 3초 후 Alert 자동 닫힘
		}
	}
</script>

<main>
	<h1>Form with Dynamic AJV Validation</h1>
	<Form>
		<FormGroup>
			<Label for="id">ID</Label>
			<Input
					type='text '
					id="id"
					bind:value={id}
					placeholder="Enter ID"
					on:input={() => validateField('id', id)}
					invalid={!!errors.id}
			/>
			{#if errors.id}
				<FormFeedback>{errors.id}</FormFeedback>
			{/if}
		</FormGroup>
		<FormGroup>
			<Label for="name">Name</Label>
			<Input
					type="text"
					id="name"
					bind:value={name}
					placeholder="Enter Name"
					on:input={() => validateField('name', name)}
					invalid={!!errors.name}
			/>
			{#if errors.name}
				<FormFeedback>{errors.name}</FormFeedback>
			{/if}
		</FormGroup>
		<FormGroup>
			<Label for="type">Type</Label>
			<Input
					type="text"
					id="type"
					bind:value={type}
					placeholder="Enter Type"
					on:input={() => validateField('type', type)}
					invalid={!!errors.type}
					feedback={errors.type}
			/>
			<!--{#if errors.type}-->
			<!--	<FormFeedback>{errors.type}</FormFeedback>-->
			<!--{/if}-->
		</FormGroup>
		<Button color="primary" on:click={saveForm}>Save</Button>
	</Form>
	<Alert
			children={alertMessage}
			color="success"
			class="fixed"
			closeAriaLabel="Close"
			dismissible
			fade
			heading="success"
			style="width:400px; top:20px"
			isOpen={showAlert}>
	</Alert>
</main>

<style lang="css">
	main {
		max-width: 400px;
		margin: 0 auto;
		padding-top: 50px;
	}

	h1 {
		text-align: center;
	}
</style>
