<script>
	//@ts-nocheck
	import { theme } from '$lib/Stores/ThemeStore';
	import Options from '$lib/Options.svg';
	import { rows } from '$lib/Stores/ScheduleStore.js';
	import { template as templateStore } from '$lib/Stores/ScheduleStore.js';
	import { languageStore, dictionary, mapping } from '$lib/Stores/LanguageStore';
	import { fullweektoogle } from '$lib/Stores/ScheduleStore.js';
	import { get } from 'svelte/store';
	import { couting_signal } from '$lib/Stores/ChangedStore.js';
	import { usernameStore } from '$lib/Stores/UserStore.js';
	import { TimetableBackendApiService } from '$lib/TimetableBackendApiService';
	import { show_error } from '$lib/Stores/PopUpStore';
	import { lessonAttributeToggleStore } from '$lib/Stores/LessonAttributeToggleStore.js';

	let focus = false;
	let colorRGB = '';
	let color = '';
	let dynamicRows = get(rows);
	let dynDays = get(fullweektoogle);
	let dynLessonAttributeToggles = get(lessonAttributeToggleStore);
	let template = 'University';
	let setting = 'Theme';
	let disabled = true;
	let wait = false;
	let theme_to_mapping = new Map();
	theme_to_mapping.set('Light', mapping.Bright);
	theme_to_mapping.set('Dark', mapping.Dark);
	theme_to_mapping.set('Pink', mapping.Pink);

	let setting_to_mapping = new Map();
	setting_to_mapping.set('Theme', mapping.Design);
	setting_to_mapping.set('Template', mapping.Template);
	setting_to_mapping.set('Days', mapping.Days);

	let template_to_mapping = new Map();
	template_to_mapping.set('University', mapping.University);
	template_to_mapping.set('School', mapping.School);
	template_to_mapping.set('Custom', mapping.Custom);

	let lessonAttribute_to_mapping = new Map();
	lessonAttribute_to_mapping.set('show_subject', mapping.Subject);
	lessonAttribute_to_mapping.set('show_teacher', mapping.Teacher);
	lessonAttribute_to_mapping.set('show_room', mapping.Room);

	let language = get(languageStore).language;
	let username = get(usernameStore);

	languageStore.subscribe((value) => {
		language = value.language;
	});

	usernameStore.subscribe((value) => {
		username = value;
	});

	let settings = ['Theme', 'Template', 'Days'];

	let themes = ['Light', 'Dark', 'Pink'];

	let templates = ['University', 'School', 'Custom'];

	let lessonAttributes = ['show_subject', 'show_teacher', 'show_room'];

	let DarkText = ['Light'];

	/**
	 * @type {Map<String, String>}
	 */
	let colormap = new Map();

	colormap.set('Light', '255, 255, 255');
	colormap.set('Dark', '12, 16, 70');
	colormap.set('Pink', '255, 0, 255');

	theme.subscribe((value) => {
		color = value;
		colorRGB = colormap.get(value);
	});

	$: {
		disabled = template !== 'Custom';
	}

	$: updateRows(dynamicRows);

	$: updateTemplate(template);

	$: updateDays(dynDays);

	$: updateLessonAttributes(dynLessonAttributeToggles);

	// eslint-disable-next-line no-unused-vars
	async function updateDays(e) {
		fullweektoogle.set(dynDays);
		if (username) {
			const { res, error } = await TimetableBackendApiService.updateMetadata({
				fullweektoogle: dynDays
			});
			if (res) {
				couting_signal.update((old) => old + 1);
			} else {
				show_error(error.message);
			}
		}
	}

	// eslint-disable-next-line no-unused-vars
	async function updateTemplate(e) {
		templateStore.set(template);
		if (template === 'Custom') {
			updateRows(dynamicRows);
		}

		const updateRows = template === 'Custom' ? dynamicRows : null;

		if (username) {
			const { res, error } = await TimetableBackendApiService.updateMetadata({
				template,
				rows: updateRows
			});
			if (res) {
				couting_signal.update((old) => old + 1);
			} else {
				show_error(error.message);
			}
		}
	}

	// eslint-disable-next-line no-unused-vars
	async function updateRows(e) {
		dynamicRows = dynamicRows < 1 ? 1 : dynamicRows;
		dynamicRows = dynamicRows > 30 ? 30 : dynamicRows;
		rows.set(dynamicRows);
		if (username) {
			const { res, error } = await TimetableBackendApiService.updateMetadata({ rows: dynamicRows });
			if (res) {
				couting_signal.update((old) => old + 1);
			} else {
				show_error(error.message);
			}
		}
	}

	// eslint-disable-next-line no-unused-vars
	async function updateLessonAttributes(e) {
		lessonAttributeToggleStore.set(dynLessonAttributeToggles);
		if (username) {
			const { res, error } = await TimetableBackendApiService.updateMetadata({
				show_room: dynLessonAttributeToggles.show_room,
				show_subject: dynLessonAttributeToggles.show_subject,
				show_teacher: dynLessonAttributeToggles.show_teacher
			});
			if (res) {
				couting_signal.update((old) => old + 1);
			} else {
				show_error(error.message);
			}
		}
	}

	function waiter() {
		wait = true;
		setTimeout(() => {
			wait = false;
		}, 100);
	}
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-static-element-interactions -->
<div
	class={focus ? 'options' : 'optionscollapse'}
	on:click={() => {
		if (focus === false && !wait) {
			focus = true;
		}
	}}
>
	{#if focus}
		<div class="top">
			{#each settings as s}
				{#if s === setting}
					<div style="background-color: black;color: white">
						{dictionary.get(setting_to_mapping.get(s))[language]}
					</div>
				{:else}
					<button
						style="--color: 0,0,0; color: white"
						on:click={() => {
							setting = s;
						}}>{dictionary.get(setting_to_mapping.get(s))[language]}</button
					>
				{/if}
			{/each}
			<button
				on:click={() => {
					waiter();
					focus = false;
				}}
				style="--color: 0,0,0; border-radius: 50rem;">❌</button
			>
		</div>
		{#if setting === 'Theme'}
			<div class="top">
				{#each themes as t}
					{#if t === color}
						<div
							style="background-color: rgb({colorRGB});color: {DarkText.includes(t)
								? 'black'
								: 'white'}"
						>
							{dictionary.get(theme_to_mapping.get(t))[language]}
						</div>
					{:else}
						<button
							style="--color: {colormap.get(t)}; color: {DarkText.includes(t) ? 'black' : 'white'}"
							on:click={async () => {
								theme.set(t);
								if (username) {
									const { res, error } = await TimetableBackendApiService.updateMetadata({
										theme: t
									});
									if (res) {
										couting_signal.update((old) => old + 1);
									} else {
										show_error(error.message);
									}
								}
							}}>{dictionary.get(theme_to_mapping.get(t))[language]}</button
						>
					{/if}
				{/each}
			</div>
		{:else if setting === 'Template'}
			<div class="inbetween">
				{#each lessonAttributes as l}
					<label>
						<input type="checkbox" bind:checked={dynLessonAttributeToggles[l]} />
						{dictionary.get(lessonAttribute_to_mapping.get(l))[language]}
					</label>
				{/each}
			</div>
			<div class="middle">
				{#each templates as t}
					<label>
						<input type="radio" bind:group={template} value={t} style="margin-right: 0.5rem" />
						{dictionary.get(template_to_mapping.get(t))[language]}
					</label>
				{/each}
			</div>
			<div class="bottom">
				<label for="rows">{dictionary.get(mapping.Hours)[language]}:</label>
				<input
					type="number"
					name="rows"
					bind:value={dynamicRows}
					{disabled}
					style={disabled ? 'cursor: not-allowed' : ''}
				/>
			</div>
		{:else if setting === 'Days'}
			<div class="middle">
				<label for="rows">7-{dictionary.get(mapping.Days)[language]}:</label>
				<input type="checkbox" bind:checked={dynDays} />
			</div>
		{/if}
	{:else}
		<img src={Options} alt="Options" />
	{/if}
</div>

<style>
	.optionscollapse {
		height: 2.5rem;
		aspect-ratio: 1/1;
		cursor: pointer;
		border: 1px solid black;
		background-color: rgba(151, 147, 147, 0.781);
		border-radius: 50%;
		padding: 2px;
		margin-top: 1rem;
	}

	.optionscollapse img {
		height: 100%;
		aspect-ratio: 1/1;
	}

	.options {
		width: 20rem;
		height: 15rem;
		background-color: rgba(151, 147, 147);
		border-radius: 1rem;
	}

	.top {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 1rem;
	}

	.top button {
		background-color: rgba(var(--color));
		border: none;
		border-radius: 5px;
		padding: 0.5rem;
		cursor: pointer;
		transition: 0.5s;
	}

	.top div {
		border-radius: 5px;
		padding: 0.2rem;
		border: 3px solid gold;
	}

	.top button:hover {
		transform: scale(1.1);
	}

	.inbetween {
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 1rem;
	}

	.middle {
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 1rem;
	}

	.bottom {
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 1rem;
	}

	.bottom label {
		margin-right: 1rem;
	}
</style>
