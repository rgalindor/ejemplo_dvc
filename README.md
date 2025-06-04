# DVC example use case

This is a getting started document about [DVC](https://dvc.org/doc/start).


## Get started

### 1. Repository pull and basic config

You can substitute this with your own repo. The purpose is to prepare a repo with the required dependencies.

```bash
git clone git@github.com:rgalindor/ejemplo_dvc.git
python -m venv .venv
source .venv/bin/activate   
pip install --upgrade pip
pip install -r requirements.txt
```

### 2. DVC setup

These commands allow the DVC setup, in the example by using gdrive folder as a remote storage.

```bash
dvc init
git add -A
git commit
dvc remote add --default myremote gdrive://xxx
dvc remote modify --local myremote gdrive_client_id 'xxx'
dvc remote modify --local myremote gdrive_client_secret 'xxx'
dvc remote modify --local myremote gdrive_acknowledge_abuse true
```

Get sure to follow [recommended steps](https://dvc.org/doc/user-guide/data-management/remote-storage/google-drive#using-a-custom-google-cloud-project-recommended) to configure your gdrive remote by creating a _desktop app_ for a new client for the **Google Drive API**. Then set up properly the auth credentials using the **client_id** and the **client_secret**.

### 3. Train and track a model

You can train a model and persist it by using `joblib.dump(model, "models/artifact.pkl")` method to save the sklearn object `model` to `models/artifact.pkl`. Then tracking it with DVC and git.


```bash
dvc add models/artifact.pkl
git add models/artifact.pkl.dvc models/.gitignore
git commit
dvc push
git add -A
git commit
```

### 4. Update the model and track it with DVC

You can create a new version of the model, then saving it using the same code for saving it. The local version of the `models/artifact.pkl` has changed, but you can add those changes as a new version through DVC. 

```bash
dvc status
dvc add models/artifact.pkl
git add models/artifact.pkl.dvc
dvc push
```

### 5. Test roll back the model

Since `git` is tracking the code and the `pkl.dvc` files, you can bring back old versions by checking out to an hypothetical `hashcommit` to then pull artifacts with `dvc`

```bash
git log
git checkout hashcommit models/artifact.pkl.dvc
dvc pull
```

Then you can rollback again to another version `original_hashcommit` and bring that artifact version again.

```bash
# another model rollback
git checkout original_hashcommit models/artifact.pkl.dvc
dvc pull
```

