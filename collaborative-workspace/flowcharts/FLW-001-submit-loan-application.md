flowchart TD
    start([Start]) --> prepare[Prepare application data]
    subgraph Partner["Partner System"]
        prepare
        submit[Submit application]
        receive[Receive receipt acknowledgement]
    end
    subgraph API["Loan Submission API"]
        authenticate[Authenticate partner credentials]
        verify[Verify partner contract]
        validate[Validate customer and spouse data]
        checkMaster[Validate collateral and loan against master data]
        checkOwner[Validate owner is customer or spouse]
        checkCoverage[Confirm location is covered]
        checkPlate[Confirm license plate has no active loan]
        checkResubmission[Confirm resubmission eligibility]
        record[Record application]
        initiate[Initiate risk scoring asynchronously]
        acknowledge[Acknowledge receipt within 2 seconds]
    end
    subgraph Risk["Risk Scoring Process"]
        score[Assign risk recommendation category]
        riskEnd([Risk recommendation ready for separate committee review])
    end
    subgraph Receipt["Partner Outcome"]
        receiptEnd([Receipt acknowledged])
    end
    prepare --> submit --> authenticate --> verify --> validate --> checkMaster --> checkOwner --> checkCoverage --> checkPlate --> checkResubmission --> record --> initiate
    initiate --> acknowledge --> receive --> receiptEnd
    initiate --> score --> riskEnd
