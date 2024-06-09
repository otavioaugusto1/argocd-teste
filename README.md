# argocd-teste

## Resumo:
O Argo CD é uma ferramenta de entrega contínua declarativa para Kubernetes, que permite gerenciar implantações de aplicativos Kubernetes usando o conceito de GitOps. Com o Argo CD, você pode sincronizar o estado do cluster com o estado declarado em um repositório Git.

### Passo 1: Instalar o Argo CD

1. **Criar um namespace para o Argo CD:**
   ```sh
   kubectl create namespace argocd
   ```

2. **Instalar o Argo CD:**
   ```sh
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

3. **Verificar se todos os pods do Argo CD estão em execução:**
   ```sh
   kubectl get pods -n argocd
   ```

### Passo 2: Expor o Argo CD Server

Para acessar a interface web do Argo CD, você precisa expor o serviço `argocd-server`.

1. **Expor o Argo CD Server usando `kubectl port-forward`:**
   ```sh
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```

Agora, você pode acessar a interface web do Argo CD em `https://localhost:8080`.

### Passo 3: Configurar o Acesso ao Argo CD

1. **Obter a senha inicial do Argo CD:**
   A senha inicial é o nome do pod do `argocd-server`. Você pode obtê-la com o comando:
   ```sh
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
   ```

2. **Fazer login na interface web:**
   - **Usuário:** `admin`
   - **Senha:** (a senha que você obteve no passo anterior)

### Passo 4: Configurar um Aplicativo com Argo CD

Agora, vamos configurar um aplicativo para ser gerenciado pelo Argo CD. Suponha que você tenha um repositório Git com a configuração do Kubernetes para um aplicativo.

1. **Criar um repositório Git com a configuração do aplicativo:**
   - Por exemplo, a estrutura do repositório pode ser:
     ```
     ├── kustomization.yaml
     ├── deployment.yaml
     └── service.yaml
     ```

2. **Adicionar o aplicativo no Argo CD:**
   - Navegue até a interface web do Argo CD.
   - Clique em `New App`.
   - Preencha os campos:
     - **Application Name:** Nome do seu aplicativo.
     - **Project:** `default`.
     - **Sync Policy:** Pode deixar como manual ou automática.
     - **Repository URL:** URL do seu repositório Git.
     - **Revision:** Branch (por exemplo, `main`).
     - **Path:** Caminho para os manifests do Kubernetes dentro do repositório.
     - **Cluster URL:** `https://kubernetes.default.svc`.
     - **Namespace:** Namespace onde o aplicativo será implantado (por exemplo, `default`).

3. **Sincronizar o aplicativo:**
   - Após criar o aplicativo, clique em `Sync` para implantar a configuração no cluster Kubernetes.

### Passo 5: Verificar a Implantação

1. **Verificar os recursos do aplicativo:**
   - Na interface web do Argo CD, você pode visualizar os recursos implantados (pods, serviços, etc.) e seu status.
   - A interface mostrará se o estado do cluster está em sincronia com o estado declarado no Git.

2. **Sincronizar alterações:**
   - Sempre que fizer alterações nos manifests do Kubernetes no repositório Git, você pode clicar em `Sync` na interface web do Argo CD para aplicar as alterações no cluster.

### Utilizando o Argo CD CLI

Além da interface web, você também pode usar a CLI do Argo CD para gerenciar seus aplicativos.

1. **Instalar a CLI do Argo CD:**
   ```sh
   brew install argocd
   ```

2. **Configurar a CLI para apontar para o Argo CD Server:**
   ```sh
   argocd login localhost:8080
   ```

3. **Criar um aplicativo usando a CLI:**
   ```sh
   argocd app create <app-name> \
     --repo <repo-url> \
     --path <repo-path> \
     --dest-server https://kubernetes.default.svc \
     --dest-namespace <namespace>
   ```

4. **Sincronizar um aplicativo usando a CLI:**
   ```sh
   argocd app sync <app-name>
   ```
