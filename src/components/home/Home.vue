// parte inicial da pagina, recebe o conteudo com os paineis // de imagem o
buscador os botoes com funcionalidade e o acesso // ao home e cadastro
<template>
  <div>
    <h1 class="centralizado">{{ titulo }}</h1>
    <p class="centralizado">{{ mensagem }}</p>
    <!-- input que recebe a busca do usuario atraves do titulo
    da imagem -->
    <input
      type="search"
      class="filtro"
      @input="filtro = $event.target.value"
      placeholder="filtre por parte do título"
    />

    <ul class="lista-fotos">
      <li class="lista-fotos-item" v-for="foto of fotosComFiltro">
        <!-- painel que recebe a imagem e os botoes de alterar e remover
     alem do titulo da imgem com a sua respectiva descrição  -->
        <meu-painel :titulo="foto.titulo">
          <imagem-responsiva
            v-meu-transform.animate="15"
            :url="foto.url"
            :titulo="foto.titulo"
          />
          <!-- botoes que acessam a função de alterar e remover o painel -->
          <router-link :to="{ name: 'altera', params: { id: foto._id } }">
            <!-- botao de alteracao -->
            <meu-botao tipo="button" rotulo="ALTERAR" />
          </router-link>
          <!-- botao de remocao -->
          <meu-botao
            tipo="button"
            rotulo="REMOVER"
            @botaoAtivado="remove(foto)"
            :confirmacao="true"
            estilo="perigo"
          />
        </meu-painel>
      </li>
    </ul>
  </div>
</template>

<script>
// importaçoes que trazem os componentes para o uso
import Painel from "../shared/painel/Painel.vue";
import ImagemResponsiva from "../shared/imagem-responsiva/ImagemResponsiva.vue";
import Botao from "../shared/botao/Botao.vue";
import transform from "../../directives/Transform";
import FotoService from "../../domain/foto/FotoService";

export default {
  // componenents sao instancias do vue reutilizaveis
  // nela sao passadas as funções criadas nos componentes
  // e dentro de template são trazidos os componentes para uso
  components: {
    "meu-painel": Painel,
    "imagem-responsiva": ImagemResponsiva,
    "meu-botao": Botao,
  },
  // sao componentes que alteram o estado de visualização
  directives: {
    "meu-transform": transform,
  },
  
  data() {
    return {
      titulo: "Alurapic",
      fotos: [],
      filtro: "",
      mensagem: "",
    };
  },

  computed: {
    fotosComFiltro() {
      if (this.filtro) {
        let exp = new RegExp(this.filtro.trim(), "i");
        return this.fotos.filter((foto) => exp.test(foto.titulo));
      } else {
        return this.fotos;
      }
    },
  },

  methods: {
    remove(foto) {
      this.service.apaga(foto._id).then(
        () => {
          let indice = this.fotos.indexOf(foto);
          this.fotos.splice(indice, 1);
          this.mensagem = "Foto removida com sucesso";
        },
        (err) => {
          this.mensagem = err.message;
        }
      );
    },
  },

  created() {
    this.service = new FotoService(this.$resource);
    this.service.lista().then(
      (fotos) => (this.fotos = fotos),
      (err) => {
        this.mensagem = err.message;
      }
    );
  },
};
</script>

<style>
.centralizado {
  text-align: center;
}

.lista-fotos {
  list-style: none;
}

.lista-fotos .lista-fotos-item {
  display: inline-block;
}

.filtro {
  display: block;
  width: 100%;
}
</style>
