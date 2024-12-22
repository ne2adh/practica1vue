<template>
    <div class="contactos">
        <h5 class="mb-5 mt-5">Contactos</h5>

        <div>
            <div class="mb-3">
                <div class="input-group">
                    <span class="input-group-text" id="name-filter"><i class="bi bi-filter"></i> Filtrar por Nombre</span>
                    <input type="text" class="form-control" v-model="toFilter" placeholder="Escribe un nombre">
                </div>
            </div>

            <div class="mb-3">
                <div class="input-group">
                    <span class="input-group-text" id="email-search-text"><i class="bi bi-search"></i> Buscar por Email</span>
                    <input type="search" class="form-control" v-model="toSearch" placeholder="Escribe un email">
                </div>
            </div>
        </div>

        <div class="card">
            <div class="card-body">
                <form @submit.prevent="save()">
                    <table class="table table-striped">
                        <thead>
                        <tr>
                            <th colspan="6">
                                <div class="row">
                                    <div class="col">
                                        <input type="text" class="form-control" placeholder="Nombre" v-model="newContact.name">
                                    </div>
                                    <div class="col">
                                        <input type="email" class="form-control" placeholder="Email" v-model="newContact.email">
                                    </div>
                                    <div class="col">
                                        <input type="text" class="form-control" placeholder="Dirección" v-model="newContact.address">
                                    </div>
                                    <div class="col">
                                        <input type="text" class="form-control" placeholder="Teléfono" v-model="newContact.phone">
                                    </div>
                                    <div class="col">
                                        <input type="text" class="form-control" placeholder="País" v-model="newContact.country">
                                    </div>
                                    <div class="col">
                                        <input type="text" class="form-control" placeholder="Ciudad" v-model="newContact.city">
                                    </div>
                                    <div class="col">
                                        <button type="submit" class="btn btn-primary">Agregar</button>
                                    </div>
                                </div>
                            </th>
                        </tr>
                        <tr>
                            <th>Nombre</th>
                            <th>Email</th>
                            <th>Direccion</th>
                            <th>Teléfono</th>
                            <th>Pais</th>
                            <th>Ciudad</th>
                            <th></th>
                        </tr>
                        </thead>

                        <tbody>
                        <tr v-for="(contact, index) in getList()" :key="index">
                            <td>{{ contact.name }}</td>
                            <td>{{ contact.email }}</td>
                            <td>{{ contact.address }}</td>
                            <td>{{ contact.phone }}</td>
                            <td>{{ contact.country }}</td>
                            <td>{{ contact.city }}</td>
                            <td>
                                <button type="button" class="btn btn-info btn-sm" @click="edit(index)"><i
                                        class="bi bi-pen"></i></button>

                                <button type="button" class="btn btn-danger btn-sm" @click="remove(index)"><i
                                        class="bi bi-trash3"></i></button>
                            </td>
                        </tr>
                        </tbody>
                    </table>
                </form>
            </div>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="editModal" tabindex="-1" aria-labelledby="editModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h1 class="modal-title fs-5" id="editModalLabel">Editar Contacto</h1>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <form @submit.prevent="saveEdit()">
                        <div class="modal-body" v-if="itemSelected">

                            <div class="mb-3">
                                <label for="updateName" class="form-label">Nombre</label>
                                <input type="text" v-model="itemSelected.name" class="form-control" id="updateName">
                            </div>
                            <div class="mb-3">
                                <label for="updateEmail" class="form-label">Email</label>
                                <input type="email" v-model="itemSelected.email" class="form-control" id="updateEmail">
                            </div>
                            <div class="mb-3">
                                <label for="updateAddress" class="form-label">Direccion</label>
                                <input type="text" v-model="itemSelected.address" class="form-control" id="updateAddress">
                            </div>
                            <div class="mb-3">
                                <label for="updatePhone" class="form-label">Telefono</label>
                                <input type="text" v-model="itemSelected.phone" class="form-control" id="updatePhone">
                            </div>
                            <div class="mb-3">
                                <label for="updateCountry" class="form-label">Pais</label>
                                <input type="text" v-model="itemSelected.country" class="form-control" id="updateCountry">
                            </div>
                            <div class="mb-3">
                                <label for="updateCity" class="form-label">Ciudad</label>
                                <input type="text" v-model="itemSelected.city" class="form-control" id="updateCity">
                            </div>

                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
                            <button type="submit" class="btn btn-primary">Guardar cambios</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
    export default {
        data() {
            return {
                newContact: {
                    name: '',
                    email: '',
                    address: '',
                    phone: '',
                    country: '',
                    city: ''
                },
                contactsArray: [
                    { name: 'Alice Johnson', email: 'alice.johnson@example.com', address: '123 Maple Street', phone: '123-456-7890', country: 'USA', city: 'New York' },
                    { name: 'Bob Smith', email: 'bob.smith@example.com', address: '456 Oak Avenue', phone: '987-654-3210', country: 'Canada', city: 'Toronto' },
                    { name: 'Carol White', email: 'carol.white@example.com', address: '789 Pine Road', phone: '555-123-4567', country: 'UK', city: 'London' },
                    { name: 'David Brown', email: 'david.brown@example.com', address: '321 Elm Street', phone: '444-555-6666', country: 'Australia', city: 'Sydney' },
                    { name: 'Emily Davis', email: 'emily.davis@example.com', address: '654 Spruce Lane', phone: '333-444-5555', country: 'USA', city: 'Los Angeles' }
                ],
                itemSelected: null,
                indexSelected: null,
                toFilter: '',
                toSearch: ''
            }
        },
        methods: {
            save() {
                if (this.newContact.name && this.newContact.email && this.newContact.address && this.newContact.phone && this.newContact.country && this.newContact.city) {
                    this.contactsArray.push({...this.newContact});
                    this.newContact = { name: '', email: '', address: '', phone: '', country: '', city: '' };
                } else {
                    alert("Por favor, completa todos los campos obligatorios.");
                }
            },
            remove(index) {
                if (confirm("Esta seguro de eliminar este contacto?")) {
                    this.contactsArray.splice(index, 1);
                }
            },
            edit(index) {
                this.itemSelected = {...this.contactsArray[index]};
                this.indexSelected = index;
                const editModal = new bootstrap.Modal(document.getElementById('editModal'));
                editModal.show();
            },
            saveEdit() {
                this.contactsArray[this.indexSelected] = {...this.itemSelected};

                const modalElement = document.getElementById('editModal');
                const modalInstance = bootstrap.Modal.getInstance(modalElement) || new bootstrap.Modal(modalElement);
                modalInstance.hide();

                this.itemSelected = null;
                this.indexSelected = null;
            },
            getList() {
                let result = this.contactsArray.filter((contact) => {
                    if (this.toSearch) {
                        return contact.email.includes(this.toSearch);
                    }
                    return true;
                });
                return result.filter((contact) => {
                    if (this.toFilter) {
                        return contact.name.includes(this.toFilter);
                    }
                    return true;
                });
            }
        }
    }
</script>

<style>
    .btn {
        margin-right: 3px;
    }
    .card{
        overflow-x: auto;
    }
</style>
