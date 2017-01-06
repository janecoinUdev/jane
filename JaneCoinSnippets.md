Mon 02 Jan 2017 11:14:38 PM CET 

## Snippets

-----


## main.h (genesis)
                                         
static const uint256 hashGenesisBlock("0x00000f070fb1246cf705cea3f8245f5f0a3abd6f751e10ca58586b8bb307630f");
static const uint256 hashGenesisBlockTestNet("0x000c763e402f2436da9ed36c7286f62c3f6e5dbafce9ff289bd43d7459327eb");


-----


## main.h 962-95

 // Protocol switch to support p2pool at JaneCoin block #0         /////////////////////////////////
        if (nHeight >= 0 || fTestNet)                           /////COMPILE TESTNET 0 WARNINGS//////////////
        {                                                          ///////////////////////////////
            // Take last bit of block hash as entropy bit
            unsigned int nEntropyBit = ((GetHash().Get64()) & 1ULL);
            if (fDebug && GetBoolArg("-printstakemodifier"))
                printf("GetStakeEntropyBit: nTime=%u hashBlock=%s nEntropyBit=%u\n", nTime, GetHash().ToString().c_str(), nEntropyBit);
            return nEntropyBit;
        }

// Before JaneCoin block #0 - get from pregenerated table
        int nBitNum = nHeight & 0xFF;
        int nItemNum = nHeight / 0xFF;

        unsigned int nEntropyBit = (unsigned int) ((entropyStore[nItemNum] & (uint256(1) << nBitNum)) >> nBitNum).Get64();
        if (fDebug && GetBoolArg("-printstakemodifier"))
            printf("GetStakeEntropyBit: from pregenerated table, nHeight=%d nEntropyBit=%u\n", nHeight, nEntropyBit);
        return nEntropyBit;
    }

    // ppcoin: two types of block: proof-of-work or proof-of-stake
    bool IsProofOfStake() const
    {
        return (vtx.size() > 1 && vtx[1].IsCoinStake());
    }

    bool IsProofOfWork() const
    {
        return !IsProofOfStake();
    }

    std::pair<COutPoint, unsigned int> GetProofOfStake() const
    {
        return IsProofOfStake()? std::make_pair(vtx[1].vin[0].prevout, vtx[1].nTime) : std::make_pair(COutPoint(), (unsigned int)0);
    }


-----


## checkpoints.cpp // not sure what these relate to //


static MapCheckpoints mapCheckpoints =
        boost::assign::map_list_of
        ( 0,     std::make_pair(hashGenesisBlock, 1360105017) )  /// ?
        ( 1, std::make_pair(uint256("0x00000e74380384c13893a18bb8645b2478e9a64577ab52424c848d0d2c48e5b3"), 1364674052) )
    ;

    // TestNet has no checkpoints
    static MapCheckpoints mapCheckpointsTestnet =
        boost::assign::map_list_of
        ( 0, std::make_pair(hashGenesisBlockTestNet, 1360105017) )
        ;

///////////////////////


## main.cpp

const char* pszTimestamp = "JANE";
        CTransaction txNew;
        txNew.nTime = 1480005017; //////////// (136010/5017) checkpoints has the unix in brackets ////
        txNew.vin.resize(1);
        txNew.vout.resize(1);
        txNew.vin[0].scriptSig = CScript() << 486604799 << CBigNum(9999) << vector<unsigned char>((const unsigned char*)pszTimestamp, (const unsigned char*)pszTimestamp + strlen(pszTimestamp));
        txNew.vout[0].SetEmpty();
        CBlock block;
        block.vtx.push_back(txNew);
        block.hashPrevBlock = 0;
        block.hashMerkleRoot = block.BuildMerkleTree();
        block.nVersion = 1;
        block.nTime    = 1480005017;
        block.nBits    = bnProofOfWorkLimit.GetCompact();
        block.nNonce   = 2166128; // !fTestNet ? 1575379 : 46534;


///////////////////////// Some ended up in my ready made .conf

-----

## init.cpp

        "  -port=<port>           " + _("Listen for connections on <port> (default: 13413 or testnet: 17778)") + "\n" +
        "  -maxconnections=<n>    " + _("Maintain at most <n> connections to peers (default: 125)") + "\n" +
        "  -addnode=<ip>          " + _("Add a node to connect to and attempt to keep the connection open") + "\n" +
        "  -connect=<ip>          " + _("Connect only to the specified node(s)") + "\n" +
        "  -seednode=<ip>         " + _("Connect to a node to retrieve peer addresses, and disconnect") + "\n" +
        "  -externalip=<ip>       " + _("Specify your own public address") + "\n" +
        "  -onlynet=<net>         " + _("Only connect to nodes in network <net> (IPv4, IPv6 or Onion)") + "\n" +
        "  -discover              " + _("Discover own IP address (default: 1 when listening and no -externalip)") + "\n" +
        "  -irc                   " + _("Find peers using internet relay chat (default: 1)") + "\n" +
        "  -listen                " + _("Accept connections from outside (default: 1 if no -proxy or -connect)") + "\n" +
        "  -bind=<addr>           " + _("Bind to given address. Use [host]:port notation for IPv6") + "\n" +
        "  -dnsseed               " + _("Find peers using DNS lookup (default: 1)") + "\n" +
        "  -cppolicy              " + _("Sync checkpoints policy (default: strict)") + "\n" +
        "  -banscore=<n>          " + _("Threshold for disconnecting misbehaving peers (default: 100)") + "\n" +
        "  -bantime=<n>           " + _("Number of seconds to keep misbehaving peers from reconnecting (default: 86400)") + "\n" +
        "  -maxreceivebuffer=<n>  " + _("Maximum per-connection receive buffer, <n>*1000 bytes (default: 5000)") + "\n" +
        "  -maxsendbuffer=<n>     " + _("Maximum per-connection send buffer, <n>*1000 bytes (default: 1000)") + "\n" +
        "  -detachdb              " + _("Detach block and address databases. Increases shutdown time (default: 0)") + "\n" +

#ifdef DB_LOG_IN_MEMORY
        "  -memorylog             " + _("Use in-memory logging for block index database (default: 1)") + "\n" +
#endif

        "  -paytxfee=<amt>        " + _("Fee per KB to add to transactions you send") + "\n" +
        "  -mininput=<amt>        " + str(boost::format(_("When creating transactions, ignore inputs with value less than this (default: %s)")) % FormatMoney(MIN_TXOUT_AMOUNT)) + "\n" +
#ifdef QT_GUI
        "  -server                " + _("Accept command line and JSON-RPC commands") + "\n" +
#endif
#if !defined(WIN32) && !defined(QT_GUI)
        "  -daemon                " + _("Run in the background as a daemon and accept commands") + "\n" +
#endif
        "  -testnet               " + _("Use the test network") + "\n" +
        "  -debug                 " + _("Output extra debugging information. Implies all other -debug* options") + "\n" +
        "  -debugnet              " + _("Output extra network debugging information") + "\n" +
        "  -logtimestamps         " + _("Prepend debug output with timestamp") + "\n" +
        "  -shrinkdebugfile       " + _("Shrink debug.log file on client startup (default: 1 when no -debug)") + "\n" +
        "  -printtoconsole        " + _("Send trace/debug info to console instead of debug.log file") + "\n" +
#ifdef WIN32
        "  -printtodebugger       " + _("Send trace/debug info to debugger") + "\n" +
#endif
        "  -rpcuser=<user>        " + _("Username for JSON-RPC connections") + "\n" +
        "  -rpcpassword=<pw>      " + _("Password for JSON-RPC connections") + "\n" +
        "  -rpcport=<port>        " + _("Listen for JSON-RPC connections on <port> (default: 13412 or testnet: 18345)") + "\n"
