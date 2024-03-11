# Running VU Server from source code on Linux

You can run VU Server from source code.

In order to do this you will need to install Python (programming language used by VU Server) and optionally Git.
(Installation utilizes pyenv and pyenv-virtualenv)
To do this you can run the following command

```zsh
brew install pyenv pyenv-virtualenv
```

Add pyenv to path
```zsh
# pyenv and pyenv-virtualenv
eval "$(pyenv init -)"
if which pyenv-virtualenv-init > /dev/null; then eval "$(pyenv virtualenv-init -)"; fi
```

Restart your terminal.

Then you can clone the VU Server repository and install project dependencies

```zsh
git clone https://github.com/SasaKaranovic/VU-Server.git
cd VU-Server
```

Install Python 3.9, create a vu-server virtual environment using 3.9, and associate the local directory with the vu-server envirionment.
```zsh
pyenv install 3.9
pyenv virtualenv 3.9 vu-server
pyenv local vu-server
```

Install vu-server python dependencies
```zsh
pip install -r requirements.txt

```


You will need to run the above steps only once.

After that you can start VU server by running `python server.py`

Now you can access VU server web GUI by navigating to http://localhost:5340 in your browser.



# Example command line usage
TO DO: Test and update the following for macOS

Ideally you would have an application/service/script that updates VU dials for you.

But in some instances you might want to update the VU dials from the command line.

This example shows how to do this.

Requirements

- You have to have VU server running.
- From the VU Server GUI; Get the UID of the dial you want to update.
- From the VU Server GUI; Get the API key you want to use for this purpose (create a new one or use existing one).


In order to update the dial, we need to make a [HTTP request](https://docs.vudials.com/api/dial_UID_set/) to the VU server.

Let's say we want to set the dial with the UID `3E0075000650564139323920` to `50%` using API key `cTpAWYuRpA2zx75Yh961Cg`

We can make a simple GET request using `wget` (or `curl` if you prefer):

```zsh
wget -O- -q "http://localhost:5340/api/v0/dial/3E0075000650564139323920/set?value=50&key=cTpAWYuRpA2zx75Yh961C" ; echo
```

You can use pipe redirects in your terminal to redirect output of applications to VU dials.

Let's say we use following command to retrieve CPU usage on our Ubuntu machine

```zsh
echo $[100-$(vmstat 1 2|tail -1|awk '{print $15}')]
```

This command will output an integer that represents CPU usage in percentage. We can then forward this information to VU server using pipe redirects

```zsh
echo $[100-$(vmstat 1 2|tail -1|awk '{print $15}')] | xargs -I{} wget -O- -q "http://localhost:5340/api/v0/dial/3E0075000650564139323920/set?value={}&key=cTpAWYuRpA2zx75Yh961C"; echo
```

Please keep in mind that this is a very "simple" example (and yet looks somewhat complicated), but the main idea is that you can easily redirect output of any application on your system and send it to VU Server to have it displayed on your VU1 dials.

Ideally you would not run these commands manually but instead have a script/service/cron that updates your dials periodically.

