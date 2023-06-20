<script>
	import { Stepper, Step } from '@skeletonlabs/skeleton';
	import PocketBase from 'pocketbase';
	const pb = new PocketBase('https://payment.pockethost.io');
	let complete = true;
	import { toastStore } from '@skeletonlabs/skeleton';
	let personal_info = {
		firstName: '',
		lastName: '',
		fatherName: '',
		mobile: ''
	};

	let address = {
		house_village: '',
		post_code: '',
		upazilas: '',
		districts: '',
		division: '',
		country: ''
	};

	let eudcation = {
		ssc_school_name: '',
		ssc_board: '',
		ssc_year: '',
		ssc_result: '',
		hsc_college_name: '',
		hsc_board: '',
		hsc_year: '',
		hsc_result: ''
	};

	let interested_subject = 'CSE';
	let payment = {
		amount: ''
	};

	let payment_verification = {
		trnx_id: ''
	};

	let files;

	let validPersonalInfo = false;
	let validAddress = false;
	let validEudcation = false;
	let validPayment = false;
	let validImage = false;
	let validPaymentVerification = false;

	$: {
		if (
			personal_info.firstName.length >= 3 &&
			personal_info.lastName.length >= 3 &&
			personal_info.mobile.length === 11 &&
			personal_info.fatherName.length >= 3
		) {
			validPersonalInfo = false;
		} else {
			validPersonalInfo = true;
		}

		if (
			address.house_village.length >= 3 &&
			address.post_code.length === 4 &&
			address.upazilas.length >= 3 &&
			address.districts.length >= 3 &&
			address.division.length >= 3 &&
			address.country.length >= 3
		) {
			validAddress = false;
		} else {
			validAddress = true;
		}

		if (
			eudcation.ssc_school_name.length >= 3 &&
			eudcation.ssc_board.length >= 3 &&
			eudcation.ssc_year.length === 4 &&
			eudcation.ssc_result.length >= 3 &&
			eudcation.hsc_college_name.length >= 3 &&
			eudcation.hsc_board.length >= 3 &&
			eudcation.hsc_year.length === 4
		) {
			validEudcation = false;
		} else {
			validEudcation = true;
		}

		if (payment.amount.length >= 1) {
			validPayment = false;
		} else {
			validPayment = true;
		}

		if (files?.length >= 1) {
			validImage = false;
		} else {
			validImage = true;
		}

		if (payment_verification.trnx_id.length >= 1) {
			validPaymentVerification = false;
		} else {
			validPaymentVerification = true;
		}
	}

	const handleSubmit = async () => {
		const data = {
			FirstName: personal_info.firstName,
			LastName: personal_info.lastName,
			FatherName: personal_info.fatherName,
			Mobile: personal_info.mobile,
			HouseVillage: address.house_village,
			PostCode: address.post_code,
			SSCSchoolName: eudcation.ssc_school_name,
			SSCYear: eudcation.ssc_year,
			HSCCollegeName: eudcation.hsc_college_name,
			HSCYear: eudcation.hsc_year,
			HSCResult: eudcation.hsc_result,
			SSCResult: eudcation.ssc_result,
			SSCBoard: eudcation.ssc_board,
			HSCBoard: eudcation.hsc_board,
			Interested: interested_subject,
			PaymentAmount: payment.amount,
			TrnxID: payment_verification.trnx_id
		};
		console.log(data);
		try {
			const formData = new FormData();
			formData.append('Image', files[0]);
			for (const [key, value] of Object.entries(data)) {
				formData.append(key, value);
			}
			console.log(formData);
			const record = await pb.collection('students').create(formData);
			console.log(record);

			complete = false;
		} catch (err) {
			console.log(err.response.data);
			for (const [key, value] of Object.entries(err.response.data)) {
				console.log(key, value);

				toastStore.trigger({
					message: `${key}: ${value.message}`
				});
			}
		}
	};
</script>

<div class="mx-4 md:mx-auto md:max-w-lg">
	{#if complete}
		<Stepper on:complete={handleSubmit}>
			<Step stepTerm="Personal Information" locked={validPersonalInfo}>
				<svelte:fragment slot="header">Personal Information</svelte:fragment>
				<div class="flex flex-col sm:flex-row">
					<label class="label p-1">
						<span>First Name</span>
						<input
							class="input variant-form-material"
							bind:value={personal_info.firstName}
							type="text"
							placeholder="ie. John"
						/>
					</label>
					<label class="label p-1">
						<span>Last Name</span>
						<input
							bind:value={personal_info.lastName}
							class="input variant-form-material"
							type="text"
							placeholder="ie. Doe"
						/>
					</label>
				</div>
				<label class="label p-1">
					<span>Father Name</span>
					<input
						bind:value={personal_info.fatherName}
						class="input variant-form-material"
						type="text"
						placeholder="ie. John Doe's Father"
					/>
				</label>
				<label class="label p-1">
					Your Phone Number
					<div
						class="input-group variant-form-material input-group-divider grid-cols-[auto_1fr_auto]"
					>
						<div class="input-group-shim">+88</div>
						<input
							bind:value={personal_info.mobile}
							type="text"
							placeholder="01xxxxxxxx"
							pattern="[0-9]{11}"
						/>
					</div>
				</label>
			</Step>
			<Step stepTerm="Address" locked={validAddress}>
				<svelte:fragment slot="header">Address Information</svelte:fragment>

				<label class="label">
					<span>House Village</span>
					<input
						bind:value={address.house_village}
						class="input variant-form-material"
						type="text"
						placeholder="House Village"
					/>
				</label>
				<label class="label">
					<span>Post Code</span>
					<input
						bind:value={address.post_code}
						class="input variant-form-material"
						type="text"
						placeholder="Post Code"
					/>
				</label>

				<div class="flex flex-col sm:flex-row">
					<label class="label p-1">
						<span>Upazilas</span>
						<input
							bind:value={address.upazilas}
							class="input variant-form-material"
							type="text"
							placeholder="Upazilas"
						/>
					</label>
					<label class="label p-1">
						<span>Districts</span>
						<input
							bind:value={address.districts}
							class="input variant-form-material"
							type="text"
							placeholder="Districts"
						/>
					</label>
				</div>
				<div class="flex flex-col sm:flex-row">
					<label class="label p-1">
						<span>Division</span>
						<input
							bind:value={address.division}
							class="input variant-form-material"
							type="text"
							placeholder="Division"
						/>
					</label>
					<label class="label p-1">
						<span>Country</span>
						<input
							bind:value={address.country}
							class="input variant-form-material"
							type="text"
							placeholder="Country"
						/>
					</label>
				</div></Step
			>
			<Step stepTerm="Education" locked={validEudcation}>
				<svelte:fragment slot="header">Educational Qualification</svelte:fragment>
				<label class="label">
					<span>SSC School Name</span>
					<input
						bind:value={eudcation.ssc_school_name}
						class="input variant-form-material"
						type="text"
					/>
				</label>
				<div class="flex flex-col sm:flex-row">
					<label class="label p-1">
						<span>SSC Board</span>
						<input
							bind:value={eudcation.ssc_board}
							class="input variant-form-material"
							type="text"
						/>
					</label>
					<label class="label p-1">
						<span>SSC Year</span>
						<input
							bind:value={eudcation.ssc_year}
							class="input variant-form-material"
							type="text"
						/>
					</label>
					<label class="label p-1">
						<span>SSC Result</span>
						<input
							bind:value={eudcation.ssc_result}
							class="input variant-form-material"
							type="text"
						/>
					</label>
				</div>

				<label class="label">
					<span>HSC College Name</span>
					<input
						bind:value={eudcation.hsc_college_name}
						class="input variant-form-material"
						type="text"
					/>
				</label>
				<div class="flex flex-col sm:flex-row">
					<label class="label p-1">
						<span>HSC Board</span>
						<input
							bind:value={eudcation.hsc_board}
							class="input variant-form-material"
							type="text"
						/>
					</label>
					<label class="label p-1">
						<span>HSC Year</span>
						<input
							bind:value={eudcation.hsc_year}
							class="input variant-form-material"
							type="text"
						/>
					</label>
					<label class="label p-1">
						<span>HSC Result</span>
						<input
							bind:value={eudcation.hsc_result}
							class="input variant-form-material"
							type="text"
						/>
					</label>
				</div>
			</Step>
			<Step stepTerm="Interest">
				<svelte:fragment slot="header">Interested in</svelte:fragment>
				<div class="space-y-2">
					<label class="flex items-center space-x-2">
						<input
							class="radio"
							type="radio"
							checked
							name="radio-direct"
							value="CSE"
							bind:group={interested_subject}
						/>
						<p>CSE</p>
					</label>
					<label class="flex items-center space-x-2">
						<input
							class="radio"
							type="radio"
							name="radio-direct"
							value="EEE"
							bind:group={interested_subject}
						/>
						<p>EEE</p>
					</label>
					<label class="flex items-center space-x-2">
						<input
							class="radio"
							type="radio"
							name="radio-direct"
							value="BBA"
							bind:group={interested_subject}
						/>
						<p>BBA</p>
					</label>
					<label class="flex items-center space-x-2">
						<input
							class="radio"
							type="radio"
							name="radio-direct"
							value="BCMB"
							bind:group={interested_subject}
						/>
						<p>BCMB</p>
					</label>
				</div>
			</Step>
			<Step stepTerm="Image" locked={validImage}>
				<svelte:fragment slot="header">Upload Your Photo</svelte:fragment>
				<p>Selected Image: {files ? files[0]?.name : 'No Image Selected'}</p>
				<input class="input" type="file" bind:files accept="image/*" />
			</Step>

			<Step stepTerm="Payment" locked={validPayment}>
				<svelte:fragment slot="header">Payment info</svelte:fragment>
				<h3>
					Send money to this number. We accept bkash, nagad, rocket
					<em>01742860106</em>
				</h3>
				<label class="label">
					<span>Amount</span>
					<input bind:value={payment.amount} class="input variant-form-material" type="text" />
				</label>
			</Step>
			<Step stepTerm="Trnx. ID" locked={validPaymentVerification}>
				<svelte:fragment slot="header">Payment verification</svelte:fragment>
				<label class="label">
					<span>Enter trnx ID</span>

					<input
						bind:value={payment_verification.trnx_id}
						class="input variant-form-material"
						type="text"
					/>
				</label>
			</Step>
			<!-- ... -->
		</Stepper>
	{:else}
		<div class="card">
			<header class="card-header"><h2>Everting seems ok!</h2></header>
			<section class="p-4">
				Thank you for providing the information. We will verfify everyting and get back to you soon.
			</section>
			<footer class="card-footer">(footer)</footer>

			<a class="block card card-hover p-4 text-center variant-filled-primary text-white" href="#"
				>Download pdf</a
			>
		</div>
	{/if}
</div>
