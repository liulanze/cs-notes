# Setup Linux OS on Hyper-V

1. Download the latest Linux image iso, any distros.
2. Open Hyper-v Manager
    - Create an external virtual switch
        - In the left panal, click the Virtual Switch Manager
        - Click new Virtual Network Switch
        - Select External
        - Click create Virtual Switch
            - Name it External
            - Make sure the `External Network`: drop down has your ethernet card selected.
    - Create a build machine VM
        - Gen 2
        - 16GB+ Memory
        - Hard drave 128GB+
        - Network adapter set to `External`
        - Attach the Linux iso and start the VM
    - Set the processor number to 8+
    - Open the administrator PowerShell window and run `Set-VMProcessor -VMName <name-assigned-to-vm> -ExposeVirtualizationExtensions $true` (empower VM launched in the VM).
3. Start the VM, install the Linux OS following the direction.
4. Open VM's terminal
    - `sudo dnf check-update`
    - `sudo dnf install net-tools`
    - `ifconfig` and note your machine ip address (from the eth0 card probably)
    - `ping www.github.com`
    - `ping www.google.com`
    - Generate a ssh key and enable sshd
        - `ssh-keygen`
        - `ls -al ~/.ssh` => `id_rsa` & `id_rsa.pub` should be there
        - On the VM:
            - `sudo dnf install openssh-server`
            - `sudo systemctl start sshd`
            - `sudo systemctl enable sshd`
        - On the Host:
            - `ssh <user@vm-ip>`
