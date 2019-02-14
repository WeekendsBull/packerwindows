# Windows Packer Templates

Simple [Packer](https://packer.io) templates for creating Windows VMs that support mainstream use cases that can easily be forked and built upon. 

This project makes use of [reusable PowerShell modules](https://github.com/windowsbox/powershellmodules) which allow for others to easily create their own Packer templates without needing to fork a bunch of scripts. Packer templates tend to vary between people and companies but the scripts used by the templates don't vary as much. This is an attempt to separate out the reusable parts from the parts that don't.

## Building a Box

1. Clone this repository
2. Install [Packer](http://packer.io) 1.3.4 or newer
3. Execute Packer (see below)

You may need to set the `iso_url` and `iso_checksum` variables as some builds expect you to have downloaded an ISO from MSDN and have no default set. A good way to handle this is to use a Packer vars file. You may also need to set the product key code in the Autounattend.xml file depending on the OS installation media chosen.

The Windows templates support a plain vanilla Windows Vagrant box or a Windows Vagrant box with a bunch of .NET development tools. The examples below are shown without installing the dev tools.

### Windows10

The plain vanilla build creates a Windows 10 box with Windows updates and not much else.
```
$ packer build windows10/packer.json
```

### Windows Server 2016

The plain vanilla build creates a Windows Server 2016 box with Windows updates and not much else other than Docker.
```
$ packer build windows2016/packer.json
```

### Windows Server 1803

The plain vanilla build creates a Windows Server 1803 box with Windows updates and not much else other than Docker.
```
$ packer build windows1803/packer.json
```

### Windows Server 2019

The plain vanilla build creates a Windows Server 2019 box with Windows updates and not much else other than Docker.
```
$ packer build windows2019/packer.json
```

### Development Tools Installation

The .NET developer Vagrant box includes everything the vanilla box includes plus the following tools:
- Powershell Packagemanagement
- Visual Studio 2017 Community Edition
- Resharper 2017
- Notepad++
- Google Chrome
- Git for Windows
- Beyond Compare
- Fiddler 4
- SoapUI
- Sql Server Management Studio
- Node JS
- CloudFoundry CLI
- Nuget CLI 

If you want the development tools add `-var devtools=true` to your Packer command line, for example:
```
$ packer build -var devtools=true windows10/packer.json
```

## Development

### Local ISOs

You may want to use a Windows ISO already downloaded because you cleared your Packer cache or for other reasons. The simplest way to use an already existing ISO on your HDD is to serve it up via Python. From the directory where the ISO exists run: `python -m SimpleHTTPServer`. Then override the `iso_url` and `iso_checksum` variables by creating a variables.json file.

```
{
  "iso_url": "http://10.0.0.207:8000/en_windows_10_multi-edition_vl_version_1709_updated_dec_2017_x64_dvd_100406172.iso",
  "iso_checksum": "f38b0aeae0756e01dcd9b1600de62416e04aa963c72cea30929141f54f1235b3"
}

```

```
$ packer build -var-file windows10/variables.json windows10/packer.json
```

### Local PowerShell Module Testing

You may want to test a WindowsBox PowerShell Modules change before committing and publishing it. The quickest way to do that is to make the necessary script changes and then provide the script to the guest VM through the `a:\` drive. Mount the locally modified script by modifying the packer.json and add a floppy files entry, for example:

```
"floppy_files": [
  "../powershellmodules/modules/WindowsBox.WinRM/WindowsBox.WinRM.psm1",
  ...
```

Then you need to temporarily change how that module is loaded, from:
```
Install-Module WindowsBox.WinRM -Force
```
to
```
Import-Module -Name a:\WindowsBox.WinRM.psm1 -DisableNameChecking -Force
```

## Thanks

This project would not be possible without a lot of work from other projects like [Packer-Windows](https://github.com/joefitzgerald/packer-windows).

## Project Values

- Modern OSs and tools
- Simplicity
- Reuse
