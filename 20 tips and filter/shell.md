# custom column
kubectl get pods <pod-name> -o custom-columns=NAME:.metadata.name,RSRC:.metadata.resourceVersion

# jsonpath (regex not supported use jq for regex)

```JSON
{
  "kind": "List",
  "items":[
    {
      "kind":"None",
      "metadata":{"name":"127.0.0.1"},
      "status":{
        "capacity":{"cpu":"4"},
        "addresses":[{"type": "LegacyHostIP", "address":"127.0.0.1"}]
      }
    },
    {
      "kind":"None",
      "metadata":{"name":"127.0.0.2"},
      "status":{
        "capacity":{"cpu":"8"},
        "addresses":[
          {"type": "LegacyHostIP", "address":"127.0.0.2"},
          {"type": "another", "address":"127.0.0.3"}
        ]
      }
    }
  ],
  "users":[
    {
      "name": "myself",
      "user": {}
    },
    {
      "name": "e2e",
      "user": {"username": "admin", "password": "secret"}
    }
  ]
}
```

https://jsonpath.com/


[start:end:step]	subscript operator	{.users[0].name}	myself
[,]	union operator	{.items[*]['metadata.name', 'status.capacity']}	127.0.0.1 127.0.0.2 map[cpu:4] map[cpu:8]
?()	filter	{.users[?(@.name=="e2e")].user.password}	secret
range, end	iterate list	{range .items[*]}[{.metadata.name}, {.status.capacity}] {end}	[127.0.0.1, map[cpu:4]] [127.0.0.2, map[cpu:8]]
''	quote interpreted string	{range .items[*]}{.metadata.name}{'\t'}{end}	127.0.0.1 127.0.0.2

`$.kind` => list
`$.items[*].metadata.name` =>["127.0.0.1", "127.0.0.2"]
`$.items[0].metadata.name`
`$.items[1,3].metadata.name`
`$.items[0:3].metadata.name`
@




kubectl get pods -o json
kubectl get pods -o=jsonpath='{@}'
kubectl get pods -o=jsonpath='{.items[0]}'
kubectl get pods -o=jsonpath='{.items[0].metadata.name}'
kubectl get pods -o=jsonpath="{.items[*]['metadata.name', 'status.capacity']}"
kubectl get pods -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.startTime}{"\n"}{end}'

kubectl get pods -o=jsonpath="{range .items[*]}{.metadata.name}{'\t'}{.status.startTime}{'\n'}{end}"

# while loop
## single line
`while true; do echo "$var"; sleep 5;done`
`while true; do echo "$var"; sleep 2m5s;done`
apple
apple

## multi line
```bash
#!/bin/bash

count=1

while [ $count -le 10 ]
do
  echo "Count is: $count"
  count=$((count+1))
done
```

# echo

```bash
var=apple
echo $var
apple
echo "myname is $var"
myname is apple
```





# > , >>

`>` is used to redirect the output of a command or a series of commands to a file. If the file does not exist, it will be created. If the file already exists, its contents will be overwritten with the new output.

`>>` is used to append the output of a command or a series of commands to a file. If the file does not exist, it will be created. If the file already exists, the new output will be added to the end of the file.

`echo "Goodbye, world!" >> output.txt`


# sleep

`sleep 2m5s`

# printenv

`printenv | grep PWD`
PWD=/Users/anandujjwal/Desktop/CKA notes 
OLDPWD=/Users/anandujjwal

`printenv PWD` // print env takes arguments too



# base64
` echo "hello" | base64 `
`echo "aGVsbG8K" | base64 -d`
`base64 input.txt > b64.txt`
`base64 -d b64.txt > org.txt`

# tr -d

`cat tom.csr | base64 | tr -d "\n"`

# wc -l

`base64 -d b64.txt | wc -l `
`cat input.txt | wc -l`


# awk
prints 2nd column of output
`ls -al | awk '{print $2}'`

# grep -A2

`cat file.txt | grep success -A2` displays two additional lines

# nslookup

`nslookup domain_name [dns_server]`
`nslookup example.com`
`nslookup -type=mx example.com`

# tail & head
## tail print last n lines of output
## head prints first n lines of output
`ls -al | awk '{print $2}' | tail -5 | head -1`

# top

When you run the top command in a terminal window, it displays a continuously updating list of processes, sorted by CPU usage by default.

# lsof
The lsof command stands for LiSt Open Files and shows open files and which process uses them.
See what is running on which port
`lsof -i :80  `
`lsof -i :8080`  

# ps-aux

The ps aux command is a tool to monitor processes running on your Linux system.
See pid, cpu , memory process wise
`ps aux | head -3`

# kill a process

`kill -9 <pid>`

# sort

This command sorts the output of the ps aux command by the third column (CPU usage) in numerical order.
`ps aux | sort -k 3 -n`
`sort -k 2 file.txt`
`sort -r file.txt` // reverse order


# until
`"until nslookup mydb.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for mydb; sleep 2; done"`
# openssl
# SYS_TIME
# shell override
# curl
# time
# sed
- nslookup
- netstat
- ping
- ifconfig
- \netstat -lntp
- which




