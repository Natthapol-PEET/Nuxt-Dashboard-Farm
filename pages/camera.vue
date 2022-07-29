<template>

<div class="container text-center">
		<div class="card shadow">
			<div class="card-body">

				<!-- Header -->
				<div class="row align-items-start">
					<div class="col-2">
						<Logo />
					</div>
					<div class="col">
						<h2>Camera farm</h2>
					</div>
				</div>

				<!-- Content -->
				<div class="row align-items-start">
					<div class="col">
						<Showpicture />
					</div>
					<div class="col">
						<h2>{{ dateTime }}</h2>
					</div>
				</div>
				
			</div>
		</div>
	</div>
</template>