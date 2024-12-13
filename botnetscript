import boto3
import paramiko
import argparse
import threading
import time

def start_machine(ip, username, key_path, local_file_path, remote_file_path):
    ssh_client = paramiko.SSHClient()
    ssh_client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

    ssh_client.connect(hostname=ip, username=username, key_filename=key_path)

    #file transfer 
    sftp = ssh_client.open_sftp()
    sftp.put(local_file_path, remote_file_path)
    print(f'File {local_file_path} has been transfered to {ip}:{remote_file_path}')
    sftp.close()
    #file execution 
    ssh_client.exec_command(f'sudo chmod +x {remote_file_path} ')
    stdin, stdout, stderr = ssh_client.exec_command(f'./filename')
    output = stdout.read().decode('utf-8')
    error = stderr.read().decode('utf-8')

    
    print(f'Command output at {ip} instance:')
    print(output)
    if error:
        print(f'Failed execution {ip} instance')
        print(error)

    ssh_client.close()


#ConexÃ£o SSH  

if __name__ == '__main__':
    username = 'ubuntu'
    key_path = './amazonkey.pem'

    instance_ids = ['i-0c7844.....', '', '']
    local_file_path = 'filename'
    remote_file_path = '/home/ubuntu/filename' 


    session = boto3.Session(profile_name='default')
    ec2_client = session.client('ec2')
    response = ec2_client.describe_instances(InstanceIds=instance_ids)


    public_ips = []
    for reservation in response['Reservations']:
        for instance in reservation['Instances']:
            public_ips.append(instance['PublicIpAddress'])

    for ip in public_ips:   

        t = threading.Thread(target= start_machine, args=(ip, username, key_path, local_file_path, remote_file_path))
        t.start()

    input()

