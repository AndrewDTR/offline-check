<script>
	async function sha256(message) {
		// encode as UTF-8
		const msgBuffer = new TextEncoder().encode(message);

		// hash the message
		const hashBuffer = await crypto.subtle.digest('SHA-256', msgBuffer);

		// convert ArrayBuffer to Array
		const hashArray = Array.from(new Uint8Array(hashBuffer));

		// convert bytes to hex string
		const hashHex = hashArray.map((b) => b.toString(16).padStart(2, '0')).join('');
		return hashHex.substring(15);
	}

	let code = $state('...');
	let status = $state('good');

	async function makeRequest() {
		try {
			const res = await fetch('https://www.google.com/generate_204', {
				method: 'GET',
				mode: 'no-cors',
				cache: 'no-store'
			});
			status = 'false';

			console.log('good hit');
		} catch (e) {
			status = await sha256(
				new Date().toLocaleString('EST', {
					year: 'numeric',
					month: 'numeric',
					day: 'numeric',
					hour: 'numeric',
					minute: 'numeric',
					second: 'numeric',
					fractionalSecondDigits: 3
				})
			);
			console.log('bad hit: ' + e);
		}
	}

	setInterval(async () => {
		makeRequest();
	}, 2000);
</script>

<div
	class={`min-h-screen w-full ${status === 'false' ? 'bg-red-400' : 'bg-green-400'} flex items-center justify-center`}
>
	<p class="font-bold text-cente text-3xl">
		{status}
	</p>
</div>
