<script lang="ts">
	import { Button, Card, Img } from "flowbite-svelte";
	import { DownloadSolid, ImageSolid, ChevronUpSolid, ChevronDownSolid, ChevronSortSolid } from "flowbite-svelte-icons";

	let fileInput: HTMLInputElement;
	let canvas: HTMLCanvasElement;
	let generated = false;
	let url = "";
	let vertical = false;

	function dataURLtoBlob(dataURL: string) {
		let binary = atob(dataURL.split(",")[1]);
		let array = [];
		for (let i = 0; i < binary.length; i++) {
			array.push(binary.charCodeAt(i));
		}
		return new Blob([new Uint8Array(array)], { type: "image/png" });
	}

	const changePhoto = (position: "top" | "bottom" | "center" | undefined = "center") => {
		const file = fileInput.files?.[0];
		const reader = new FileReader();
		const profileFrame = new Image();

		if (file) {
			reader.onload = (e) => {
				const img = new Image();
				img.onload = () => {
					const ctx = canvas.getContext("2d");
					const hRatio = canvas.width / img.width;
					const vRatio = canvas.height / img.height;
					const ratio = Math.max(hRatio, vRatio);

					vertical = ratio <= hRatio;

					const centerShift_x = (canvas.width - img.width * ratio) / 2;
					const centerShift_y =
						position == "center"
							? (canvas.height - img.height * ratio) / 2
							: position == "bottom"
							? canvas.height - img.height * ratio
							: 0;
					ctx?.clearRect(0, 0, canvas.width, canvas.height);

					profileFrame.onload = () => {
						ctx?.drawImage(img, 0, 0, img.width, img.height, centerShift_x, centerShift_y, img.width * ratio, img.height * ratio);
						ctx?.drawImage(profileFrame, 0, 0, profileFrame.width, profileFrame.height, 0, 0, canvas.width, canvas.height);

						generated = true;
						const blob = dataURLtoBlob(canvas.toDataURL("image/png"));
						url = URL.createObjectURL(blob);
					};

					profileFrame.src = "profileFrame.png";
				};
				img.src = e.target?.result as string;
			};

			reader.readAsDataURL(file as Blob);
		}
	};
</script>

<div class="w-full min-h-screen flex flex-wrap justify-center gap-4 p-8">
	<div class="w-full max-w-lg mt-8">
		<Card size="lg">
			<div class="flex flex-col gap-4">
				<img src="coverPhoto.jpg" alt="Cover" class="w-full h-auto" />

				<Button color="alternative" on:click={() => fileInput.click()} fullWidth><ImageSolid class="mr-2" /> Choose Image</Button>
				<div class="hidden">
					<input
						type="file"
						accept="image/*"
						bind:this={fileInput}
						on:change={() => changePhoto()}
						class="block w-full disabled:cursor-not-allowed disabled:opacity-50 focus:border-primary-500 focus:ring-primary-500 dark:focus:border-primary-500 dark:focus:ring-primary-500 bg-gray-50 text-gray-900 dark:bg-gray-600 dark:placeholder-gray-400 border-gray-300 dark:border-gray-500 text-sm rounded-lg border !p-0 dark:text-gray-400"
					/>
				</div>

				<div class="hidden">
					<canvas bind:this={canvas} width="1080" height="1080" class="rounded-lg border overflow-hidden w-full" />
				</div>

				{#if generated}
					<Img src={url} />
					<p>You can save this photo. On mobile, press and hold this photo then save image.</p>
				{/if}

				<div class="grid grid-cols-3 gap-3" class:!hidden={!vertical || !generated}>
					<div class="col-span-3 text-sm">Position image:</div>
					<Button color="light" on:click={(e) => changePhoto("top")}><ChevronUpSolid class="w-3 h-3" /></Button>
					<Button color="light" on:click={(e) => changePhoto("center")}><ChevronSortSolid class="w-4 h-4" /></Button>
					<Button color="light" on:click={(e) => changePhoto("bottom")}><ChevronDownSolid class="w-3 h-3" /></Button>
				</div>
			</div>
		</Card>
	</div>
</div>
