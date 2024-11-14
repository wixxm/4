#!/bin/bash

YOUR_USERNAME="${{ github.actor }}"
YOUR_REPO="wikjxwrt-packages"
TARGET_REPO="https://github.com/$YOUR_USERNAME/$YOUR_REPO.git"
TARGET_DIR="${GITHUB_WORKSPACE}/$YOUR_REPO"

git config --global user.name "${{ github.actor }}"
git config --global user.email "${{ secrets.GITHUB_EMAIL }}"

rm -rf $TARGET_DIR
mkdir -p $TARGET_DIR
cd $TARGET_DIR

git clone "$TARGET_REPO" .
default_branch=$(git remote show origin | sed -n '/HEAD branch/s/.*: //p')
git checkout -B "$default_branch"

sync_repo_folder() {
  local repo_url=$1
  local branch=$2
  local folder_name=$3
  local target_folder=$4

  TEMP_DIR=$(mktemp -d)
  git clone --depth=1 --branch $branch --filter=blob:none --sparse $repo_url $TEMP_DIR
  cd $TEMP_DIR
  git sparse-checkout set $folder_name

  mkdir -p "$TARGET_DIR/$target_folder"
  rsync -av --exclude='.git' "$folder_name/" "$TARGET_DIR/$target_folder"

  cd ..
  rm -rf $TEMP_DIR
}

sync_repo_folder "https://github.com/sbwml/luci-app-mosdns.git" "v5" "mosdns" "mosdns"
# 更多同步操作...

git add .
git commit -m "Automated sync by GitHub Actions"
git push "$TARGET_REPO" "$default_branch"
