<script>
    export default {
        data: () => ({
            currentNote: '',
            notes: [],
            storageKey: 'notes2'
        }),
        watch: {
            notes: {
                handler(value) {
                    this.setLocationStorage(this.storageKey, JSON.stringify(value))
                    this.currentNote = ''
                },
                deep: true
            }
        },
        mounted() {
            this.getNotesFromStorage()
            this.setNoteFocus()
        },
        methods: {
            getNotesFromStorage() {
                const storage = localStorage.getItem(this.storageKey)
                if (storage) this.notes = JSON.parse(storage)
            },
            getNoteData() {
                return {
                    id: Date.now(),
                    text: this.currentNote,
                    checked: false
                }
            },
            setNoteFocus() {
                this.$refs.currentNote.focus()
            },
            setLocationStorage(key, value) {
                localStorage.setItem(key, value)
            },
            addNote() {
                if (this.currentNote) this.notes.unshift(this.getNoteData())
                this.setNoteFocus()
            },
            removeNote(id) {
                this.notes = this.notes.filter(note => note.id !== id)
            },
            checkNote(id) {
                this.notes.forEach(note => {
                    if (note.id === id) note.checked = !note.checked
                })
            },
            getNoteText(note) {
                return note.checked ? note.text.strike() : note.text
            }
        }
    }
</script>

<template>
    <div class="notes-view">
        <div class="controls">
            <input
                ref="currentNote"
                placeholder="Enter note..."
                @change="addNote"
                v-model="currentNote"
            >
            <button @click="addNote">
                Add note
            </button>
        </div>
        <p class="notes-display">
            <div v-for="note in notes" :key="note.id">
                <p
                    v-html="getNoteText(note)"
                    @click="checkNote(note.id)"
                    @dblclick="removeNote(note.id)"
                ></p>
            </div>
        </p>
    </div>
</template>

<style scoped>
    input[type=checkbox] {
        margin-top: 1rem;
        background: red;
        height: 15px;
        width: 15px;
    }
</style>
