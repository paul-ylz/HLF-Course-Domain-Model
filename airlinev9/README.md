# org.acme.airline

# Airline v9

https://hyperledger.github.io/composer/reference/acl_language.html

Refer to lecture on Access Control Language


#1 Create the BNA archive
composer archive create  --sourceType dir --sourceName ../

#2 Deploy the archive to runtime
composer network deploy -a ./airlinev9@0.0.1.bna -c PeerAdmin@hlfv1 -A admin -S adminpw

admin>> org.hyperledger.composer.system.NetworkAdmin#admin

#3 DO NOT - Import the card
composer card delete -n admin@airlinev9
composer card import -f admin@airlinev9.card

#4 Add a new participants

- John Doe (johnd) is the Network Administrator
composer participant add -d '{"$class":"org.acme.airline.participant.ACMENetworkAdmin","participantKey":"johnd","contact":{"$class":"org.acme.airline.participant.Contact","fName":"John","lname":"Doe","email":"john.doe@acmeairline.com"}}' -c admin@airlinev9

- Will Smith (wills) works in the Logistics department
composer participant add -d '{"$class":"org.acme.airline.participant.ACMEPersonnel","participantKey":"wills","contact":{"$class":"org.acme.airline.participant.Contact","fName":"Will","lname":"Smith","email":"will.smith@acmeairline.com"}, "department":"Logistics"}' -c admin@airlinev9

composer participant add -d '{"$class":"org.acme.airline.participant.ACMEPersonnel","participantKey":"susanm","contact":{"$class":"org.acme.airline.participant.Contact","fName":"Susan","lname":"Milo","email":"susan.milo@acmeairline.com"}, "department":"Accounting"}' -c admin@airlinev9
#5 Issue the identities
composer identity issue -u johnd -a org.acme.airline.participant.ACMENetworkAdmin#johnd -c admin@airlinev9
composer identity issue -u susanm -a org.acme.airline.participant.ACMEPersonnel#susanm -c admin@airlinev9
composer card delete -n johnd@airlinev9
composer card import -f johnd@airlinev9.card

composer identity issue -u wills -a org.acme.airline.participant.ACMEPersonnel#wills -c admin@airlinev9

composer card delete -n wills@airlinev9
composer card import -f wills@airlinev9.card

#6 Ping BNA using the johnd & wills cards
    - composer network ping -c johnd@airlinev9
    - composer network ping -c wills@airlinev9

#6 Setup the permissions.acl
    - johnd     Is the Network Administrator for airlinev9
                Should be able to execute network commands

    - wills     Works for the Logistics department
                Should NOT be able to execute any network command

#7 Rebuild the archive
composer archive create  --sourceType dir --sourceName ../

#8 Update the Network
composer network update -a ./airlinev9@0.0.1.bna -c admin@airlinev9


composer-rest-server -c johnd@airlinev9 -n always -w true