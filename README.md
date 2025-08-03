# JZero Blockchain Node Deployment

A Docker-based deployment setup for running a JZero blockchain node with both execution and consensus layer clients.

## Overview

This project provides a complete setup for deploying a JZero blockchain node using:
- **Execution Layer**: Reth (Ethereum execution client)
- **Consensus Layer**: Lighthouse (Ethereum consensus client)

The JZero network is a testnet blockchain with Chain ID 209, configured for proof-of-stake consensus.

## Architecture

The deployment consists of two main services:
- `reth`: Execution layer client handling transactions and state
- `beacon`: Consensus layer client managing validators and consensus

## Prerequisites

- Docker and Docker Compose installed
- At least 8GB RAM available
- 80GB+ free disk space for blockchain data
- Stable internet connection

## Quick Start

1. **Clone and navigate to the project directory**
   ```bash
   git clone <repository-url>
   cd jzero-node
   ```

2. **Configure environment variables**
   ```bash
   cp .env.example .env
   # Edit .env with your specific configuration
   ```

3. **Start the node**
   ```bash
   docker-compose up -d
   ```

4. **Check node status**
   ```bash
   docker-compose logs -f
   ```

## Configuration

### Environment Variables (.env)

| Variable | Description | Default |
|----------|-------------|---------|
| `FEE_RECIPIENT` | Address to receive block rewards | `0x5266Dfa5ae013674f8FdC832b7c601B838D94eE6` |
| `CHAIN_ID` | JZero network chain ID | `209` |
| `EL_BOOTNODE` | Execution layer bootnode | Pre-configured |
| `CL_BOOTNODE` | Consensus layer bootnode | Pre-configured |
| `NODE_PUBLIC_IP` | Your node's public IP (optional) | Auto-detected |

### Network Configuration

- **Chain ID**: 209
- **Network Name**: JZero Testnet
- **Consensus**: Proof of Stake (Ethereum 2.0 compatible)
- **Block Time**: 12 seconds
- **Deposit Contract**: `0x5454545454545454545454545454545454545454`

## Services

### Reth (Execution Layer)
- **Port 8545**: HTTP RPC endpoint
- **Port 8546**: WebSocket RPC endpoint  
- **Port 30303**: P2P networking
- **Data Directory**: `./data/execution-data`

### Lighthouse (Consensus Layer)
- **Port 5052**: Beacon API endpoint
- **Port 9000**: P2P networking
- **Data Directory**: `./data/consensus-data`

## API Endpoints

Once running, you can interact with your node:

### Execution Layer (Reth)
```bash
# Get latest block number
curl -X POST -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' \
  http://localhost:8545

# Get node info
curl -X POST -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":1}' \
  http://localhost:8545
```

### Consensus Layer (Lighthouse)
```bash
# Get beacon chain head
curl http://localhost:5052/eth/v1/beacon/headers/head

# Get node version
curl http://localhost:5052/eth/v1/node/version
```

## Monitoring

### Check Sync Status
```bash
# Execution layer sync status
curl -X POST -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}' \
  http://localhost:8545

# Consensus layer sync status  
curl http://localhost:5052/eth/v1/node/syncing
```

### View Logs
```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f reth
docker-compose logs -f beacon
```

## Data Persistence

Blockchain data is stored in the `./data/` directory:
- `./data/execution-data/`: Execution layer blockchain data
- `./data/consensus-data/`: Consensus layer blockchain data
- `./data/lighthouse/`: Lighthouse configuration and keys

## Maintenance

### Update Node Software
```bash
docker-compose pull
docker-compose up -d
```

### Backup Data
```bash
# Stop services
docker-compose down

# Backup data directory
tar -czf jzero-backup-$(date +%Y%m%d).tar.gz data/

# Restart services
docker-compose up -d
```

### Reset Node Data
```bash
# WARNING: This will delete all blockchain data
docker-compose down
rm -rf data/
docker-compose up -d
```

## Troubleshooting

### Common Issues

1. **Port conflicts**: Ensure ports 8545, 8546, 5052, 9000, 30303 are available
2. **Insufficient disk space**: Monitor `./data/` directory size
3. **Network connectivity**: Check firewall settings for P2P ports
4. **Sync issues**: Allow time for initial sync (can take several hours)

### Useful Commands
```bash
# Check container status
docker-compose ps

# Restart specific service
docker-compose restart reth

# View resource usage
docker stats

# Clean up unused Docker resources
docker system prune
```

## Security Considerations

- Keep your `.env` file secure and never commit it to version control
- Regularly update Docker images for security patches
- Consider firewall rules for exposed ports
- Monitor logs for unusual activity

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## Support

For issues and questions:
- Check the troubleshooting section above
- Review Docker and service logs
- Consult the official Reth and Lighthouse documentation

## License

This project is licensed under the MIT License - see the LICENSE file for details.