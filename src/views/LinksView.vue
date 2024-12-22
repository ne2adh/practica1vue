<template>
    <div class="about">
        <h5 class="mb-5 mt-5">Mis links</h5>

        <div>
            <div class="mb-3">
                <div class="input-group">
                    <span class="input-group-text" id="type-filter"><i
                            class="bi bi-filter "></i>  Filtrar por tipo </span>
                    <select class="form-select" @change="toFilter = $event.target.value">
                        <option value="">todos</option>
                        <option v-for="type in types" :value="type" :key="type">{{type}}</option>
                    </select>
                </div>
            </div>

            <div class="mb-3">
                <div class="input-group">
                    <span class="input-group-text" id="description-search-text"><i class="bi bi-search"></i> Buscar en descripción </span>
                    <input type="search" class="form-control" id="description-search"
                           @search="this.toSearch = $event.target.value">
                </div>
            </div>
        </div>

        <div class="card">
            <div class="card-body">
                <form @submit.prevent="save()">
                    <table class="table table-striped">
                        <thead>
                        <tr>
                            <th colspan="2">

                                <select class="form-select" aria-label="Default select example" v-model="newLink.type">
                                    <option v-for="type in types" :value="type" :key="type">{{type}}</option>
                                </select>

                            </th>
                            <th>

                                <input type="text" class="form-control" id="description" v-model="newLink.description">

                            </th>
                            <th>
                                <input type="url" class="form-control" id="link" v-model="newLink.url">
                            </th>
                            <th>
                                <button type="submit" class="btn btn-primary">Agregar</button>
                            </th>
                        </tr>
                        <tr>
                            <th scope="col">#</th>
                            <th scope="col">Tipo</th>
                            <th scope="col">Descripción</th>
                            <th scope="col">URL</th>
                            <th></th>
                        </tr>
                        </thead>

                        <tbody>
                        <tr v-for="(link, index) in getList()" :key="index">
                            <th scope="row">{{1+index}}</th>
                            <td>{{link.type}}</td>
                            <td>{{link.description}}</td>
                            <td><a :href="link.url">{{link.url}}</a></td>
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
                        <h1 class="modal-title fs-5" id="editModalLabel">Editar</h1>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <form @submit.prevent="saveEdit()">
                        <div class="modal-body" v-if="itemSelected">


                            <div class="mb-3">
                                <label for="updateType" class="form-label">Tipo de link</label>
                                <select class="form-select" v-model="itemSelected.type" id="updateType">
                                    <option v-for="type in types" :value="type">{{type}}</option>
                                </select>
                            </div>
                            <div class="mb-3">
                                <label for="updateDescription" class="form-label">Descripción</label>
                                <input type="text" v-model="itemSelected.description" class="form-control"
                                       id="updateDescription">
                            </div>
                            <div class="mb-3">
                                <label for="updateUrl" class="form-label">URL</label>
                                <input type="text" v-model="itemSelected.url" class="form-control" id="updateUrl">
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
                types: ['frontend', 'backend', 'system', 'library'],
                newLink: {
                    type: '',
                    description: '',
                    url: ''
                },
                linksArray: [
                    {
                        type: 'frontend',
                        description: 'Vue.js (o simplemente Vue) es un framework progresivo de JavaScript utilizado para construir interfaces de usuario (UI) y aplicaciones web modernas.',
                        url: 'https://vuejs.org/'
                    },
                    {
                        type: 'frontend',
                        description: 'Bootstrap es una biblioteca de herramientas de código abierto optimizadas para el diseño de sitios y aplicaciones web.',
                        url: 'https://getbootstrap.com/docs/5.3/getting-started/introduction/'
                    },
                    {
                        type: 'backend',
                        description: 'JSON Server es un módulo NPM muy útil, de código abierto y publicado bajo la licencia MIT, que permite a los desarrolladores crear prototipos de su Back-end realmente rápido, creando una API REST falsa a partir de un archivo JSON simple.',
                        url: 'https://www.npmjs.com/package/json-server'
                    },
                    {
                        type: 'system',
                        description: 'Node.js es un entorno de tiempo de ejecución de JavaScript.',
                        url: 'https://nodejs.org/en'
                    },
                    {
                        type: 'library',
                        description: 'Axios es una biblioteca de JavaScript que permite realizar solicitudes HTTP desde un navegador web o Node.js.',
                        url: 'https://axios-http.com/es/docs/intro'
                    }
                ],
                itemSelected: null,
                indexSelected: null,
                typeFilter: '',
                toFilter: '',
                toSearch: ''
            }
        },
        methods: {
            save() {
                // Validar que los campos no estén vacíos
                if (this.newLink.type && this.newLink.description && this.newLink.url) {
                    // Agregar un nuevo objeto al array
                    /**
                     * const newObj {type: this.newlink.type}
                     * 
                     * const newObj = {...this.newLink}
                     * */
                    this.linksArray.push({...this.newLink});
                    // Limpiar los campos del formulario
                    this.newLink.type = '';
                    this.newLink.description = '';
                    this.newLink.url = '';
                } else {
                    alert("Por favor, completa todos los campos.");
                }
            },
            remove(index) {
                if (confirm("¿Está seguro de eliminar este ítem?")) {
                    this.linksArray.splice(index, 1);
                }
            },
            edit(index) {
                this.itemSelected = {...this.linksArray[index]};
                this.indexSelected = index;
                const editModal = new bootstrap.Modal(document.getElementById('editModal'));
                editModal.show();
            },
            saveEdit() {
                this.linksArray[this.indexSelected] = {...this.itemSelected};

                const modalElement = document.getElementById('editModal');
                const modalInstance = bootstrap.Modal.getInstance(modalElement) || new bootstrap.Modal(modalElement);
                modalInstance.hide();

                this.itemSelected = null;
                this.indexSelected = null;
            },
            getList() {
                let result = this.linksArray.filter((item) => {
                    if (this.toSearch) {
                        return item.description.includes(this.toSearch);
                    }
                    return true;
                });
                return result.filter((item) => {
                    if (this.toFilter) {
                        return item.type === this.toFilter;
                    }
                    return true;
                });
                // return this.linksArray;
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