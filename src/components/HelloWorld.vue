<template>
    <v-container>
        <v-row>
            <v-col>
				<TextFieldEx v-model="value1" />
            </v-col>
            <v-col>
				<TextFieldEx v-model="value2" hint="Click to show keyboard" persistent-hint />
            </v-col>
        </v-row>
        <v-row>
            <v-col>
				<TextFieldEx v-model="value3" prefix="prefix" />
            </v-col>
            <v-col>
				<TextFieldEx v-model="value4" hide-details dense suffix="%" />
            </v-col>
			<v-col>
				<v-text-field hide-details dense />
			</v-col>
        </v-row>
    </v-container>
</template>

<script>
	import TextFieldEx from '@/components/SoftwareNumKeyboard'
	export default {
		name: 'HelloWorld',
		components: {
			TextFieldEx
		},
		data: function () {
			return {
				value1: "0",
				value2: "1",
				value3: "2",
				value4: "3",
			}
		}
	}
</script>
