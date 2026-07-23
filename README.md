# RAG Local - Clone NotebookLM

Système RAG (Retrieval-Augmented Generation) 100% local, sans aucun appel à une API externe.

## Objectif

Une application qui permet de charger ses propres documents (PDF, TXT, MD) et d'interagir avec eux de deux façons :

1. **Mode "Recherche Sémantique pure"** : cherche les passages les plus proches en sens dans les documents, sans génération de texte.
2. **Mode "Assistant RAG complet"** : utilise un LLM local pour répondre en se basant uniquement sur le contenu des documents.

## Stack technique

- **Streamlit** : interface graphique
- **LangChain** : orchestration du pipeline (extraction, découpage, recherche)
- **Chroma** : base de données vectorielle
- **HuggingFace Embeddings** (`all-MiniLM-L6-v2`) : transforme le texte en vecteurs
- **Ollama** + **phi3:mini** : modèle de langage local pour la génération

Tout tourne en local : aucune donnée n'est envoyée à une API externe (pas d'OpenAI, pas de service cloud).

## Installation

### 1. Installer Ollama

Télécharger et installer depuis [ollama.com](https://ollama.com), puis :

```bash
ollama pull phi3:mini
```

### 2. Installer les dépendances Python

```bash
pip install -r requirements.txt
```

## Lancer l'application

```bash
streamlit run app.py
```

L'application s'ouvre automatiquement dans le navigateur sur `http://localhost:8501`.

## Utilisation

1. Dans la barre latérale, charger un ou plusieurs documents (PDF, TXT, MD)
2. Cliquer sur **"Indexer les documents"** : extraction, découpage en chunks, et vectorisation
3. Poser une question dans la zone de chat
4. **Toggle désactivé** : affiche les extraits bruts trouvés dans les documents, avec la source
5. **Toggle activé** : le LLM génère une réponse basée sur ces extraits, avec un menu dépliable pour vérifier les sources utilisées

## Fonctionnement du pipeline

1. **Extraction** : lecture du texte via `PyMuPDFLoader` (PDF) ou `TextLoader` (texte)
2. **Chunking** : découpage en segments de 800 caractères avec un chevauchement (overlap) de 100 caractères, pour éviter de couper une idée en deux
3. **Vectorisation** : chaque segment est transformé en vecteur et stocké dans Chroma, avec le nom du fichier source conservé en métadonnée
4. **Recherche** : la question est vectorisée et comparée aux segments stockés (`similarity_search`, top 3 résultats)
5. **Génération** (mode RAG complet) : les segments trouvés sont injectés dans un prompt strict qui interdit au LLM d'inventer une réponse en dehors du contexte fourni

## Aléas rencontrés pendant le développement

- Syntaxe `cat << EOF` (Linux/Mac) incompatible avec PowerShell sous Windows
- Version récente de LangChain (1.3.14) : `Ollama`, `HuggingFaceEmbeddings` et `PromptTemplate` déplacés dans des packages séparés (`langchain_ollama`, `langchain_huggingface`, `langchain_core.prompts`)
- Modèle LLM choisi volontairement léger (`phi3:mini` plutôt que `mistral`) pour limiter la charge sur la machine
- Avertissement de dépréciation sur `langchain-community` (non bloquant)

## Auteur

Lovasoa Aramis
