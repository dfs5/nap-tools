# build Converter Tool docker image
sudo DOCKER_BUILDKIT=1 docker build --no-cache --secret id=nginx-crt,src=nginx-repo.crt --secret id=nginx-key,src=nginx-repo.key -t app-protect-converter .

# verify
sudo docker images

# update Signatures and TC packages - START HERE!!!
sudo docker build --no-cache -t app-protect-converter -f NAPupdateSIG-TC .

# verify
sudo docker images

# generate latest Signature Report to enrich Grafan Dashboard
sudo docker run -v ~/nap-tools/get-signatures:/tmp/convert app-protect-converter /opt/app_protect/bin/get-signatures -o /tmp/convert/signatures-report_2022-07-20.json

# copy report into nap-dashboard/signatures and run python script
cd ..
ll nap-dashboard/signatures
cp nap-tools/get-signatures/signatures-report_2022-07-28.json nap-dashboard/signatures/
cd nap-dashboard/
python3 signatures/upload-signatures.py signatures/signatures-report_2022-07-28.json localhost
