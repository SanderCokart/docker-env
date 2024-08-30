FROM node:20-slim AS base
ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
RUN corepack enable
COPY . /app

RUN apt-get update -y

# The node image does not come with ca-certificates installed but is required for requests made by Sentry.
RUN apt-get install -y ca-certificates

FROM base AS build
WORKDIR /app

# <editor-fold desc="build args">

# Standalone is required for docker images
ENV NEXT_OUTPUT="standalone"

ARG API_URL="UNSUCCESSFUL"
# </editor-fold>

RUN --mount=type=cache,id=pnpm-tracking,target=/pnpm/store pnpm install --frozen-lockfile

# Disable telemetry during build time
ENV NEXT_TELEMETRY_DISABLED 1

RUN npm run build

FROM base
WORKDIR /app
COPY --from=build /app/node_modules /app/node_modules

# Disable telemetry during runtime
ENV NEXT_TELEMETRY_DISABLED 1

# Add nextjs user and group
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

# Automatically leverage output traces to reduce image size
# https://nextjs.org/docs/advanced-features/output-file-tracing
COPY --from=build --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=build --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

CMD ["node", "server.js"]