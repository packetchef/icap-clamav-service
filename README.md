# Build me (scanner)
`docker build -t packetchef/icap-clamav-server:1.0 .`

# Run me (scanner)
`docker run --rm -d -p 1344:1344 --name scanner packetchef/icap-clamav-server:1.0`

# Test me
`c-icap-client -v -i icap.clamav.server.example.com -s avscan -f eicar.txt`

# Stuff that doesn't quite work
The Dockerfile used to call: `echo "Default Service avscan" >> /etc/c-icap/c-icap.conf`

Dockerfile also used to include: `sed -i 's/^Service antivirus_module/Service avscan/' /etc/c-icap/virus_scan.conf && \`

Which we left as, `Service antivirus_module` and instead added a `ServiceAlias avscan antivirus_module`

Right now, the scanner Docker build pulls signature updates with `freshclam`.  Future state, scanner will just be the scanner, and updater will be responsible for pulling signatures and sharing with the cluster through a Persistent Volume Claim mounted at /var/lib/clamav.  The updater container could stay running constantly, but ideally should be scheduled as a task that runs on a periodic basis.

We should also consider whether a scanner container should run just one c-icap server/process/thread, and let the cluster scale out as needed.  I'm also not sure if running entrypoint.sh is the right approach to starting up c-icap, but for now it works.

Also need to solve for scanner logging.  Right now, c-icap logs to /var/log/c-icap inside the container.  This should log instead to stdout.

