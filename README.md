# cloudinit

# <h4> Installing libguestfs-tools only required once, prior to first run </h4>
sudo apt update -y
sudo apt install libguestfs-tools -y
# <h4> Remove existing image in case last execution did not complete successfully </h4>
rm focal-server-cloudimg-amd64.img
# <h4> Download ubuntu 20.04 clouinit img </h4>
wget https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-amd64.img
# <h4> Install quemu guest agent inside the img </h4>
sudo virt-customize -a focal-server-cloudimg-amd64.img --install qemu-guest-agent
# <h4> Create cloudinit vm </h4>
sudo qm create 9000 --name "ubuntu-2004-cloudinit-template" --memory 2048 --cores 2 --net0 virtio,bridge=vmbr0
sudo qm importdisk 9000 focal-server-cloudimg-amd64.img local-lvm
sudo qm set 9000 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-9000-disk-0
sudo qm set 9000 --boot c --bootdisk scsi0
sudo qm set 9000 --ide2 local-lvm:cloudinit
sudo qm set 9000 --serial0 socket --vga serial0
sudo qm set 9000 --agent enabled=1
# <h4> Convert to template </h4>
sudo qm template 9000
# <h4> Clean up </h4>
rm focal-server-cloudimg-amd64.img
echo "next up, clone VM, then expand the disk"
echo "you also still need to copy ssh keys to the newly cloned VM"
