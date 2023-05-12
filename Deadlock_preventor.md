def CheckProcess(processes, avail, maximum, allot):
    need = []
    for i in range(len(processes)):
        need.append([0] * len(avail))
        for j in range(len(avail)):
            need[i][j] = maxmimum[i][j] - allot[i][j]

    complete = [False] * len(processes)

    work = [i for i in avail]
    safe_seq = []

    while False in complete:
        found = False
        for i, proc in enumerate(processes):
            if not complete[i] and all(need[i][j] <= work[j] for j in range(len(avail))):
                found = True
                complete[i] = True
                safe_seq.append(proc)
                for j in range(len(avail)):
                    work[j] += allot[i][j]
                break

        if not found:
            return (False, [])

    return (True, safe_seq)


if _name_ == "_main_":
    processes = list(range(5))
    avail = [3, 3, 2]
    maximum = [
        [7, 5, 3],
        [3, 2, 2],
        [9, 0, 2],
        [2, 2, 2],
        [4, 3, 3],
    ]
    allot = [
        [0, 1, 0],
        [2, 0, 0],
        [3, 0, 2],
        [2, 1, 1],
        [0, 0, 2],
    ]

    need = []
    for i in range(len(processes)):
        need.append([0] * len(avail))
        for j in range(len(avail)):
            need[i][j] = maximum[i][j] - allot[i][j]

    print("Initial system state:")
    print("Processes:", processes)
    print("Available resources:", avail)
    print("Maximum resource need:")
    for i in range(len(maximum)):
        print(f"\tProcess {i}: {maximum[i]}")
    print("Current allocation:")
    for i in range(len(allot)):
        print(f"\tProcess {i}: {allot[i]}")
    print()

    while True:
        req_proc = int(input("Enter process requesting resource (-1 to exit): "))
        if req_proc == -1:
            break

        req_res = list(map(int, input("Enter requested resources: ").split()))

        if all(req_res[i] <= avail[i] - allot[req_proc][i] for i in range(len(avail))):
            for i in range(len(avail)):
                avail[i] -= req_res[i]
                allot[req_proc][i] += req_res[i]
                need[req_proc][i] -= req_res[i]

            safe, seq = CheckProcess(processes, avail, maximum, allot)
            if safe:
                print("Request granted")
                print("Safe sequence:", seq)
            else:
                print("Request denied (system may not operate safely )")
                print("Reversing resource allocation")
                for i in range(len(avail)):
                    avail[i] += req_res[i]
                    allot[req_proc][i] -= req_res[i]
                    need[req_proc][i] += req_res[i]
        else:
            print("Request denied (not enough resources available)")